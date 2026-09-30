# Superset Workout Timer — Design

Single static HTML file (`index.html`), no build step, no dependencies. Vanilla JS/CSS bundled inline. localStorage only, no backend.

## Config screen (shown before Start, on idle)

Four number inputs, defaults shown, locked once a session starts:

| Field | Default |
|---|---|
| Supersets | 3 |
| Exercises per superset (same count for all supersets) | 6 |
| Rest between exercises | 01:00 |
| Rest between supersets | 03:00 |

## State machine (single primary button)

Phases: `idle(config) → exercise → rest → exercise → rest → … → idle`

- **Start** — exercise timer begins at 00:00, counts up. Button label → "Rest".
- **Rest** — logs the just-finished exercise entry (`S{n} E{m} - mm:ss`). Rest timer counts down from the applicable default (see below), continues past 00:00 into negative (`-mm:ss`) if overrun. Button label → "Next".
- **Next** — logs the rest entry (`Rest - mm:ss`, actual elapsed, always positive even if the countdown went negative). Advances exercise index: `E{m+1}` within the same superset, or rolls to `S{n+1} E1` once the configured exercises-per-superset count is exhausted. New exercise timer starts at 00:00. Button label → "Rest".
- **Rest default selection** (automatic, no prompt): if the exercise that just finished was the **last** exercise in its superset, the following rest uses the **superset-rest** default (03:00); otherwise it uses the **exercise-rest** default (01:00).
- **Next disables itself** once the last configured exercise (final superset, final exercise) has already been logged and there is nothing left to advance to — signals the user to press **Finish** instead.

## Finish button (separate, always clickable regardless of phase)

Ends whichever phase is currently running (exercise or rest), logs that final entry (even if 00:00), computes:
- `E:` sum of all exercise durations
- `R:` sum of all rest durations (each stored as actual positive elapsed time, not the negative countdown display)
- Total wall time = E + R

Appends one formatted entry to the permanent log (see format below), then resets the page to the config screen for a new session.

## Log format (matches existing example)

```
2026-09-29 - 59:01 (E: 34:21, R: 23:45) [Copy]
S1 E1 - 48:34
Rest - 01:23
S1 E2 - 01:54
Rest - 01:12
...
S1 E6 - 01:54
Rest - 03:15
S2 E1 - 48:34
...
```

Each historical entry renders with its own **Copy** button that copies that entry's full text block to the clipboard (`navigator.clipboard.writeText`).

## Storage (localStorage, two keys)

- `timer.session` — live in-progress state: config values, phase, superset/exercise index, phase-start epoch timestamp (ms), entries logged so far this session. Timers are always recomputed from the stored epoch timestamp on load/tick, never from a paused in-memory counter — so a page reload mid-exercise or mid-rest resumes with the correct elapsed/remaining time.
- `timer.log` — array of finished sessions (each a fully rendered entry as above + raw data), persisted indefinitely until manually cleared. Survives reload and new sessions; never auto-pruned.

## Export

**Export MD** button dumps the entire `timer.log` history as one `.md` file download, newest session first. For a single session, use that entry's **Copy** button instead (per-entry copy, no separate per-entry export needed).

## Wake Lock

`navigator.wakeLock.request('screen')` on Start; released on Finish (return to idle). Re-acquired on `visibilitychange` if the tab regains foreground mid-session (Wake Lock auto-releases when a tab is hidden — no way around that natively; best-effort re-acquire only).

## Edge cases

- Rest overrun displays negative (`-00:23`) but the logged value is the true positive elapsed duration.
- Reload during exercise or rest resumes exact phase/timer from stored epoch timestamp — no data loss.
- Finish pressed immediately after Start/Rest with ~00:00 elapsed still logs that zero-ish entry rather than dropping it.
- Next is disabled after the true last exercise of the workout is logged — Finish is the only valid action at that point.

## Verification plan (no test framework — static single-page app)

Manual hand-verification in browser before calling done:
1. Full Start → Rest → Next cycle across superset boundaries (confirms 01:00 vs 03:00 rest default switch and index rollover).
2. Reload mid-exercise and mid-rest (confirms `timer.session` epoch-based resume).
3. Finish mid-exercise and mid-rest (confirms partial entry logged + totals correct).
4. Per-entry Copy and full Export MD (confirms clipboard + file download content matches format).
5. Wake Lock requested on Start, released on Finish (confirms via `navigator.wakeLock` state / no sleep during a long-running manual test).

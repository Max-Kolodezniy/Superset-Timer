# Superset Timer

A single-file browser-based workout timer for supersets: exercise → rest → exercise → rest, logged automatically, exported as Markdown. No install, no build step, no backend — open `index.html` and go.

After a workout just copy and paste it to our app or AI to analyze.

Don't lose any data: it stays on your device until you delete it. No Cloud sync, no Subscription, no BS!

**Live:** https://max-kolodezniy.github.io/Superset-Timer/

## Screenshots

| Config + Log | Exercise | Rest | Rest overtime | Log entry expanded |
|---|---|---|---|---|
| [![Configuration screen and log overview](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/1%20-%20configuration.jpg)](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/1%20-%20configuration.jpg) | [![Exercise timer running](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/2%20-%20excercise.jpg)](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/2%20-%20excercise.jpg) | [![Rest timer counting down](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/3%20-%20rest.jpg)](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/3%20-%20rest.jpg) | [![Rest overtime, flipped red](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/4%20-%20rest%20over.jpg)](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/4%20-%20rest%20over.jpg) | [![Expanded log entry with timeline background](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/5%20-%20log%20entry.jpg)](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/5%20-%20log%20entry.jpg) |

## Features

- **Configurable structure** — supersets (1–9), exercises per superset (1–19), separate rest durations for between-exercise and between-superset (0–900s, step 10), with +/- steppers, scroll adjustment, and values remembered across visits.
- **One-button flow** — Start begins the exercise timer counting up ([exercise phase](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/2%20-%20excercise.jpg)); the same button becomes Rest ([counts down](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/3%20-%20rest.jpg), flips to [overtime red](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/4%20-%20rest%20over.jpg) past 00:00) then Next, advancing through the superset/exercise sequence automatically. On the final exercise, the button is replaced by Finish.
- **Finish / Cancel** — Finish ends the session from any phase and logs it, even mid-exercise or mid-rest. Cancel discards the in-progress session without logging anything.
- **Session log** — every finished session gets an entry: date, start–end time, totals (exercise / rest / overall). Click a header to [expand the full per-exercise/per-rest breakdown](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/5%20-%20log%20entry.jpg). Each entry has its own Copy (clipboard) and Delete (with confirm) actions; Clear Log wipes everything (with confirm).
- **Export as .md** — dumps the full log history to a downloadable Markdown file, newest first.
- **Timeline visualization** — each log entry's header renders a background stripe of the whole session: blue for exercise, green for rest, red for any rest that ran over its target ([example](https://max-kolodezniy.github.io/Superset-Timer/docs/interface/1%20-%20configuration.jpg)). Hover a segment for its label and duration.
- **Stamina bar** — the live timer digits sit over a fill bar: blue growing over 60s during exercise, green-to-red during rest as it approaches the end of the rest window.
- **Fullscreen toggle** and **Wake Lock** (keeps the screen on during a session, re-acquired automatically if the tab regains focus).
- **Persistent & resumable** — session state, log history, and config are all kept in `localStorage`. Reloading mid-workout resumes the exact phase and elapsed/remaining time from a stored timestamp, not a paused counter. Multiple tabs stay in sync.
- **OLED-friendly** — pure black background.

## Usage

Open `index.html` in a browser (or visit the GitHub Pages link above). Set the four config values, hit Start, and follow the button through the workout. Hit Finish whenever the session ends.

No dependencies, no dev server required — it's one HTML file with inline CSS/JS.

## Tech

Vanilla HTML/CSS/JS, `localStorage` for persistence, CSS Container Queries for responsive timer sizing, `Screen Wake Lock API`, `navigator.clipboard`. Tested on mobile, tablet, and desktop; tablet is the primary target device.

## License

AGPLv3 — see [LICENSE](LICENSE). Commercial use is allowed; if you run a modified version as a network service, you must make that version's source available to its users.

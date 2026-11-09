# LiftLog

A free gym workout tracker that anyone can use on their phone. The owner is a student, not a professional web developer: explain things in simple English and keep steps short.

## Files
- `index.html`: the entire app (HTML + CSS + JavaScript in one file, no frameworks, no build step)
- `manifest.json`: makes the app installable ("Add to Home Screen")
- `sw.js`: service worker so the app works offline
- `icon-180.png`, `icon-192.png`, `icon-512.png`: app icons

## Hosting and storage
- Hosted on GitHub Pages (free, static). No server, no database.
- All data is saved in the browser's localStorage under the key `liftlog-v1`. Each person's data stays on their own phone. Many people can use the same link without seeing each other's data.

## Data shape (must stay backward compatible, people already have data saved)
```
state = { v:1, unit:"kg"|"lb", theme:"auto"|"light"|"dark", rest:seconds, bar:number,
  days:[{id, name, color, exercises:[{id, name}]}],
  logs:[{id, date, started, dayId, dayName, color, entries:[{name, sets:[{w, r}]}]}],
  active: null | {dayId, dayName, color, started, items:[{id, name, sets:[{w, r, pw, pr, done}]}]} }
```
- Exercise history is linked across days by exercise name (lowercased), not by id.
- Optional fields added in the first release (still v:1, `normalize()` in index.html fills defaults, so data without them still works):
  - top level: `alert` (bool, sound/vibration when rest ends), `lastBackup` (ms timestamp), `backupSnooze` (ms timestamp), `accent` (app color key: purple, blue, green, orange, pink, teal), `name` (optional first name for the greeting and profile)
  - `days[].exercises[].sets`: number of sets to start with (1 to 10, default 3 when missing)
  - `days[].exercises[].reps` and `active.items[].reps`: rep target text like "8-12" or "5"
  - `days[].exercises[].rest` and `active.items[].rest`: rest seconds for that exercise (missing = use top-level `rest`)
  - `days[].exercises[].ss` and `active.items[].ss`: superset group id. Exercises with the same id are done back to back; rest only starts after the last one in the group. Groups with fewer than 2 members are cleaned up by `cleanGroups()`.
  - `active.dayId` is null for an empty workout (not started from a split day)
  - `days[].exercises[].bw`, `logs[].entries[].bw`, `active.items[].bw`: true for bodyweight exercises (w = extra weight)
  - `logs[].note`, `active.note`: workout notes
  - `logs[].date` and `logs[].started` are ms timestamps. `active.items[].sets` w/r are strings while typing, numbers once saved in logs.
- Migrations live in `normalize()` (called by `load()` and by backup import).

## Hosting details
- Repo: github.com/ame6er/GymTracker. Live: https://ame6er.github.io/GymTracker/
- GitHub Pages serves the `main` branch, root folder. All paths in the app are relative so it works under /GymTracker/.
- Test locally with `python3 -m http.server 8000`, at 390px wide.

## Current features
Build-your-own split (no ready-made splits), split days shown as cards on home with a preview sheet, start an empty workout, searchable exercise list with body part filters and custom exercises, per-exercise options (sets, rep target, own rest time, bodyweight, order), "add weight" tip when the rep target was hit on every set, app color choice, supersets (in the split and during a workout), profile page (name, week streak, totals, workouts per week chart, best lifts), workout table with a Previous column and rest times between sets, Finish button in the header, set logging with "last time" shown as placeholder hints, rest timer, plate calculator, new-record detection on finish, history, progress chart (heaviest set, estimated 1RM, volume), kg/lb, light/dark mode, backup export/import as a JSON file.

## Design
Minimal. The owner loves minimalism: small buttons, lots of space, buttons side by side instead of stacked, text links for secondary actions, flat accent color (no big gradients). Each exercise shows a simple line drawing (`PICS` and `PIC_RULES` in index.html pick one by exercise name). Small touches of life: week dots, set tick animation, confetti on new records. Do not copy another app's art or look.

## Rules for every change
- Keep the app plain HTML/CSS/JS with no build step, so GitHub Pages can serve the files as they are.
- NEVER break existing saved data. If the data shape must change, write a migration in `load()` that upgrades old data and bump `v`.
- Every time any file changes, bump the `CACHE` name in `sw.js` (liftlog-v1 to liftlog-v2, and so on) so phones get the update.
- All text shown to users: simple English, sentence case, no em dashes.
- Mobile-first. Test at a 390px wide screen.
- Do only what is asked. No extra features, libraries or refactors.

## Stop and ask the owner before
- Creating a GitHub repo or pushing anything
- Deleting or renaming any file
- Changing the data shape or the localStorage key

## How to report
After each step, one line: ✅ what was finished, or ❌ what failed and what the owner needs to do. At the end, a short list of what's done and the live link.

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

## Current features
Starter plans (Push/Pull/Legs, Upper/Lower, Full body, blank), editable days and exercises, set logging with "last time" shown as placeholder hints, rest timer, plate calculator, new-record detection on finish, history, progress chart (heaviest set, estimated 1RM, volume), kg/lb, light/dark mode, backup export/import as a JSON file.

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

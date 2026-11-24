# Logbook & Importer

Single-file web app (`index.html`: HTML, CSS and vanilla JS in one file, no build, no tests) that converts
HeliCrew Manager exports into LogTen Pro flights. Repo `VonTedworth/logbook-importer`; live at
https://vontedworth.github.io/logbook-importer/ (GitHub Pages from `main`, root).

**At session start read `HANDOVER.md`, especially "Next up".** Do not read `README.md` each session; it is the
user-facing record. Read it only for the rule you are about to change, and update it when behaviour changes.
End each batch with `/wrap` (or `/checkpoint` mid-batch).

## Invariants
- Never change: the storage keys in `LS`, the backup tag `sector-log-backup`, the LogTen `serviceID`
  `uk.worsley.sectorlog`, the calibration key `SL-CALIBRATION`. A new address or key means empty or orphaned data.
- Every shipped change bumps `BUILD` (`n`, `date`) in `index.html` and the build number in README. `/wrap` does it.
- Total comes from In - Out, not HCM's Total column. Send is blocked for unknown places, unknown regs and scenes
  without a grid. The app never creates LogTen places or aircraft and never sends coordinates.
- Pushing deploys. Do not push without the user's go-ahead; `/wrap` is the push.

## Build, test, deploy
- No build step and no automated tests. Do not claim tests passed. To check a change, run
  `python3 -m http.server 8791 --bind 127.0.0.1` and load http://127.0.0.1:8791/index.html (a `file://` or
  snapshot origin has no usable localStorage), then exercise the flow in the browser.
- Cheap syntax check: extract the inline `<script>` and run `node --check` on it (see `/wrap`).
- Deploy: push to `main`; GitHub Pages (legacy build, no workflow) publishes in a minute or two.

## File map
- `index.html` (about 65 KB, the whole app). Do not read it whole; grep for the `/* ===== ... ===== */` section
  headers and read one section: configuration & persistence, small utils, file intake, remark parsing, rotor times,
  build sectors, render, LogTen payload, calibration, backup / restore, mapping & alias modals, unrecognised place
  resolution, aircraft logic, unknown aircraft resolution. `KNOWN_SITES` and `LS` are in the first section.
- `README.md` user-facing rules and derivations. `HANDOVER.md` state, invariants, next steps.
- `.claude/skills/` `/wrap` and `/checkpoint` (tracked). `.claude/settings.local.json` is ignored.

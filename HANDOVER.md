# HANDOVER — Logbook & Importer

Single-file web app (`index.html`, vanilla JS, no build) that turns the two HeliCrew Manager exports (Excel + LogTen
CSV) into LogTen Pro flights via the `logten://` URL scheme. Private use by Ed (GAMA KSS HEMS, AW169).
Live: https://vontedworth.github.io/logbook-importer/ (GitHub Pages, legacy build from `main`, root).
Repo: `VonTedworth/logbook-importer` (renamed from `sector-log` on 2026-11-24). User-facing detail: `README.md`.

## Status
- Build **18** (rename + setup) is pushed; nothing unpushed. Build 17 added the Backup button.
- No automated tests. Verify by loading the page (`python3 -m http.server`, any port) and checking the build stamp
  on the device after deploy.

## Derivation rules (one line each; README has the reasoning)
- Rows join on date + local Out + local In. AirMaestro placeholder rows dropped.
- Zulu times from the Excel (L)-(Z) offset per sector; midnight rollover by Out <= Off <= On <= In ordering.
- **Total = In - Out** whenever both rotor times exist; HCM's Total column is not trusted (HCM TOT chip is info only).
- Identical rotor/airborne times -> Out -5 min, In +1 min, flag `ROTOR +6`. A manual time edit overrides it.
- Capacity defaults SIC (toggle P1u/s). Takeoff/landing counts come only from the `1x ...` remark lines.
- NVG T/O and Ldg: at night, only at Redhill (EGKR) and HEMS scenes. Platform T/O/Ldg from the site's `rooftop` flag.
- Scenes (grid refs) -> `KSS HEMS SITE` with the grid in remarks; departure scenes inherit the earlier arrival's
  grid; a missing grid blocks Send. Unknown places and unknown regs block Send until resolved.
- `flight_key = HCM-<epoch>` so a re-send updates the LogTen entry. Pax is optional and omitted when empty.

## Invariants
- Storage keys (`LS` in index.html: `sl_mapping3`, `sl_aliases`, `sl_imported`, `sl_calibrated`, `sl_aircraft`,
  `sl_excluded`, `sl_usersites`), the backup format tag `sector-log-backup`, the LogTen `serviceID`
  `uk.worsley.sectorlog` and the calibration flight key `SL-CALIBRATION` never change despite the rename.
- Every change that ships bumps `BUILD` (`n` and `date`) in index.html and the build number in README.
- The app never creates LogTen places/aircraft and never sends coordinates.
- A new address means empty on-device storage: use the header **Backup** button to carry settings across.

## Recent
- **2026-11-24 build 17**: Backup button (export/import all `sl_*` settings as JSON, validated, replace + reload).
- **2026-11-24 build 18, rename + setup**: app renamed "Logbook & Importer" (title, header, apple-mobile-web-app-title,
  `APP_META.application`, README); repo renamed to `logbook-importer` via `gh`, local `origin` updated, Pages URL
  followed automatically (no deploy workflow, nothing else to change). The old `.../sector-log/` address no longer
  serves. Added `.gitignore` (`.DS_Store`, `.claude/settings.local.json`), this file, `CLAUDE.md`, and the `/wrap`
  and `/checkpoint` skills adapted from Collective. There is no web manifest, so a home-screen install takes its name
  from the `apple-mobile-web-app-title` meta when re-added.

## Next up
- On the iPhone: confirm the new address shows build 18 and "Logbook & Importer", Backup -> Import the saved text
  (if not already done), then re-add to Home Screen so the icon takes the new name.
- Confirm `flight_paxCount` lands in LogTen's Total Pax on first real import (attribute name uncalibrated).
- Not built: own-logbook file output (JSON/CSV to iCloud) and the CAA-format print page (see README).

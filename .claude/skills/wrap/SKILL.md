---
name: wrap
description: End-of-batch wrap-up for Logbook & Importer. Bumps the build number, updates HANDOVER.md and README, syntax-checks the script, then commits and pushes (deploying to GitHub Pages) and prints a short summary. There are no automated tests and it says so. Use only when the user types /wrap.
disable-model-invocation: true
---

# /wrap — close a batch

Run from the repository root. Be terse: no narration between steps.

1. **Look at the batch.** `git status --short`, `git diff --stat`, `git log origin/main..HEAD --oneline`.
   Do not read `index.html` whole (65 KB source); grep or read one section if you must.
2. **Build number.** The app uses build numbers, not semver. Bump `BUILD = { n, date }` in `index.html` by one and set
   `date` to today (`date +%F`), and update "Current build" in `README.md` to match. Skip the bump only if the batch
   changed neither `index.html` nor `README.md` behaviour (docs or tooling only) and say so. Never change the storage
   keys, `BACKUP_TAG`, `serviceID` or `SL-CALIBRATION`.
3. **README.md.** If behaviour or a derivation rule changed, update the relevant section in place (it is the
   user-facing record). Otherwise leave it.
4. **HANDOVER.md.** Update in place, keep it short (under 6 KB, check `wc -c`):
   - Status: build number and what is unpushed (after this, nothing).
   - Replace the "Recent" entry with this batch's: what changed, decisions, anything rejected or deliberately not
     done. Keep one line for the previous build if useful.
   - Update "Derivation rules" or "Invariants" only if they changed.
   - **Last, write or refresh `## Next up`**: bullets for what the next session needs (follow-ups, flagged issues,
     things found but not fixed, agreed next steps, device checks after deploy). Remove items now done. If a `WIP:`
     checkpoint commit is in the batch, its note is replaced by this batch's entry.
5. **Check quietly.** There are **no automated tests**; say so in the summary, never invent any. Do a syntax check only
   (extracts the inline script, then parses it):
   `python3 -c "import re,os;h=open('index.html').read();open(os.environ.get('TMPDIR','/tmp')+'/lbi-check.js','w').write(re.findall(r'<script>(.*?)</script>',h,re.S)[-1])" && node --check "${TMPDIR:-/tmp}/lbi-check.js" && echo SYNTAX OK`
   If it fails, stop: do not commit or push, show the error and leave the tree as it is.
6. **Commit.** `git add -A` after checking `git status` shows no unexpected files (never `.DS_Store`,
   `.claude/settings.local.json`, secrets). `.claude/skills/` must be tracked. Message: `build <n>: <short title>`
   plus a few lines of what and why. End with the attribution line the session instructions specify.
7. **Push.** `git push origin main`. That deploys via GitHub Pages; do not poll the build. If rejected, stop and report;
   never force-push. Then `git rev-parse HEAD` and `git ls-remote origin main` should match.
8. **Summary** (five lines at most): build number, commit hash, "no automated tests; syntax check passed",
   HANDOVER.md size, and the live address https://vontedworth.github.io/logbook-importer/ with a reminder that Pages
   takes a minute or two and the device should show the new build stamp (reload or reopen the home-screen app).
   Mention anything left undone.

---
name: checkpoint
description: Save mid-batch state so the session can be cleared without releasing. Updates HANDOVER.md (where the batch stands, plus "Next up") and commits locally with a "WIP:" message. No build bump, no push. Use when the user types /checkpoint or asks to checkpoint before /clear.
---

# /checkpoint — save the batch mid-way

Run from the repository root. Be terse. This is not a release: **no build bump, no push, no deploy.**

1. **Look.** `git status --short`, `git diff --stat`, `git log origin/main..HEAD --oneline`. Do not read `index.html`
   whole (65 KB source).
2. **HANDOVER.md** (keep under 6 KB, check `wc -c`):
   - Under "Recent", replace any earlier "Batch in progress" note with a short current one: what the batch is, what is
     done, what is half-done or untested, decisions so far, anything rejected. Mark it clearly as a checkpoint with the
     build number not bumped.
   - Write or refresh `## Next up`: unpushed work, follow-ups, flagged issues, agreed next steps, and the first thing
     to do on resuming. Remove items that are done.
3. **Commit locally.** The app has no automated tests, so none are run. Stage with `git add -A` after checking
   `git status` for unexpected files (never `.DS_Store`, `.claude/settings.local.json`, secrets). Message:
   `WIP: <short title>` plus a few lines on what is in and what is not. End with the attribution line the session
   instructions specify. Nothing else: no `git push`.
4. **Tell the user** in three lines at most: the commit hash, that it is local only (not pushed, not deployed, build
   number unchanged), and that it is safe to `/clear`; the next session starts by reading HANDOVER.md "Next up".
   `/wrap` later replaces the checkpoint note and releases.

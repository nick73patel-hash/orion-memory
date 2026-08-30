---
name: pref-save-command
description: "When Nick says \"save\", run all four actions — update memory, update session log, commit + push, back up"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 95d1d1db-85af-4a17-ae21-ba340d6d9256
  modified: 2026-08-30T20:13:32.038Z
---

**When Nick says "save" (or "update, save, push, back up" in any combination), it means ALL FOUR of these — not just the one he named:**

1. **Update the memory files** — fold in every decision, correction and fact from the conversation since the last save. Not just the headline: the reversals, the things he overrode, the open questions.
2. **Update `session_log.md`** — a new entry at the top (newest first), covering the work since the last entry.
3. **Commit and push** the memory repo to `origin main`.
4. **Back up** to `C:\Users\ducat\Projects\orion-memory-backups\` as `<name>_YYYY-MM-DD.md` — at minimum `session_log`, `MEMORY`, and every `project_*` / `env_*` / `pref_*` file touched.

**Why:** Nick was asking for these individually and having to name each one. Set 2026-08-30 — he asked that "save" always trigger the full set.

**How to apply:**
- Don't ask which parts he wants. Do all four.
- Check `git diff --stat` looks proportional after writing the session log — a scripted edit that converts LF→CRLF turns a small diff into a whole-file rewrite (see [[env-windows-machine]]).
- Confirm the push succeeded and report the commit SHA.

Related: [[env-windows-machine]] for the git/shell specifics.

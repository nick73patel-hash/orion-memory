---
name: pref-delegate-subagents
description: Default to delegating build/implementation tasks to BACKGROUND subagents so Orion stays free to converse.
metadata: 
  node_type: memory
  type: feedback
  originSessionId: ebf25fc6-c665-448b-ab23-e71b7275270d
  modified: 2026-08-02T23:10:28.098Z
---

The user wants Orion to **delegate substantive work to subagents, run in the BACKGROUND**, on essentially every request — so Orion stays available to chat and the user can work on something else in parallel.

**Why:** The user (bro) juggles many businesses at once and treats Orion as a partner he converses with continuously. Blocking foreground work makes Orion unavailable; background subagents keep the conversation flowing while builds run. He explicitly asked for this (2026-08).

**How to apply:**
- For any real build/implementation task (features, endpoints, UI, migrations-as-code, refactors, multi-file edits), dispatch a subagent with `run_in_background: true` and immediately tell the user it's running — then keep talking. Relay the result when the task-notification arrives. Never fabricate a pending result.
- Fan out multiple background subagents in parallel when tasks are independent (different files); keep one agent for a single coherent feature (see the file-conflict reasoning — parallel only when files don't overlap).
- Match model to task: routine build → default/Sonnet; AI-feature / security-sensitive / heavy → Opus 5 (see [[pref-model-opus5]]).
- Orion still handles DIRECTLY (no subagent): quick answers/questions, git commit + push of finished work, giving the user SQL/migrations to run, and genuinely trivial one-line edits where spinning up an agent is slower.
- Subagents have occasionally stalled on API errors mid-task — if one dies, Orion finishes it himself and reports honestly.

Related: [[project_perpetual_blue]] (the CRM this pattern is mostly used on), [[session_log]].

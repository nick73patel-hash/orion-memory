---
name: pref-model-opus5
description: When to use Opus 5 vs keep Opus 4.8 as the working model
metadata: 
  node_type: memory
  type: feedback
  originSessionId: ebf25fc6-c665-448b-ab23-e71b7275270d
  modified: 2026-07-29T17:50:52.008Z
---

Default working model is **Opus 4.8**. The user gave a standing green light to move up to **Opus 5** whenever it will benefit the work — but Claude cannot switch its own main-thread model (that's the user's `/model` control).

**How to apply:**
- **Main thread:** proactively flag the moment a task genuinely benefits from Opus 5 and tell the user to switch via `/model`. Don't nag — only when it moves the needle.
- **Subagents:** Claude *can* set a stronger model per dispatched agent, so run heavy agents on Opus 5 directly without asking.

**Bump to Opus 5 for:** the Perpetual Blue CRM AI features (recipe photo→structured extraction, preference-sheet consolidation, weekly menu + shopping-list generation), gnarly multi-file debugging, security-sensitive design, big architectural decisions.

**Stay on 4.8 for:** routine CRUD screens, wiring click handlers, nav/styling fixes, mechanical edits.

Related: [[project_condo_assistant]] and the Perpetual Blue CRM work (see [[session_log]]).

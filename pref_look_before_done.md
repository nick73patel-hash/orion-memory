---
name: pref-look-before-done
description: "Build it, then LOOK at it — visually verify UI in Claude in Chrome before calling anything done"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 95d1d1db-85af-4a17-ae21-ba340d6d9256
  modified: 2026-09-01T05:12:48.250Z
---

**Never call a UI change done without looking at it.** Set 2026-08-31, when Nick pointed out that two days of CRM work — capital pages, Jobs summary cards, print stylesheet, the decision-brief artifact, a formatted email — had all been shipped on structural checks alone and never actually viewed.

**Why:** `tsc`, `npm run build` and tag-balance checks prove a page *renders*. They do not catch a squashed column, a bad wrap, or **a number that is simply wrong**. The very first visual review found a real bug the build could never have caught: the capital page reported **$24,926.81 "of which estimated"** when only **$9,500** was a genuine estimate — the view was conflating *estimated* with *not yet evidenced*, making $15K of documented spend look as soft as two placeholders.

**How to apply:**
- **Use Claude in Chrome** (`mcp__claude-in-chrome__*`) — it drives Nick's real Chrome with his live sessions. The sandboxed in-app browser (`mcp__Claude_Browser__*`) **cannot** reach the CRM, private artifacts, or render local HTML files properly; they all need a login it does not have.
- Workflow: `list_connected_browsers` → `tabs_context_mcp {createIfEmpty:true}` → `navigate` → `screenshot`. Batch with `browser_batch`.
- **Read the numbers on screen, not just the layout.** Cross-check totals against what memory says they should be — that is how the estimate bug surfaced, and how the Mercury $100 was confirmed to have landed correctly.
- Page-zoom shortcuts (`ctrl+0`) are not supported; use the `zoom` action with a region instead. Nick's window runs ~991px wide, so check narrow-viewport behaviour.
- It is his real browser with live sessions — stay on what was asked for, and say what is being opened.

Related: [[env-windows-machine]], [[project-perpetual-blue]].

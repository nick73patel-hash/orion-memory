---
name: pref-copy-buttons
description: "Copy buttons don't work in show_widget's sandbox — use files opened in a native app instead"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 95d1d1db-85af-4a17-ae21-ba340d6d9256
  modified: 2026-08-29T19:51:07.668Z
---

**Never ship a copy button inside a `show_widget`.** The widget iframe is sandboxed and clipboard writes are blocked. Tried three times on 2026-08-29; none reached the clipboard.

**Why:** Nick pasted stale clipboard content into the Supabase SQL editor and got `42601: syntax error at or near "Sean"` — the widget had said **"copied"** when nothing had. The `document.execCommand('copy')` fallback returns `true` whether or not anything lands, so reporting success from its return value is a lie.

**How to apply:**
- **Deliver copyable text as a file**, then open it in a native app: `Start-Process notepad.exe "<path>"` → Ctrl+A, Ctrl+C. This is the route that actually worked.
- Plain `.txt` files (via [[pref-delegate-subagents]]-style `SendUserFile`) and fenced code blocks in chat are the reliable fallbacks — both let Nick select text himself.
- If a copy button is unavoidable, **only claim success when the async `navigator.clipboard.writeText` promise resolves.** Otherwise select the text and say "press Ctrl+C" — never assert a copy you cannot confirm.
- Artifact pages are a separate sandbox and *may* allow clipboard, but this has **never been verified** — the browser pane renders local files as static snapshots, so the buttons can't be clicked to test. Don't promise they work.

Related: [[env-windows-machine]], [[project-perpetual-blue]].

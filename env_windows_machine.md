---
name: env-windows-machine
description: "Nick's Windows machine quirks — broken Bash path, PowerShell has no permission rules, no Python, where Node/ExcelJS lives"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 2ba035ee-bd06-45c1-bbbc-a749b8dffa53
  modified: 2026-08-29T14:14:03.200Z
---

Environment facts for Nick's Windows 11 machine that keep costing time when rediscovered.

**Bash tool — root cause found 2026-08-29.** Claude Code resolves Git Bash to `C:\Program Files\Git\bin\..\usr\bin\bash.exe`, which collapses to `C:\Program Files\Git\usr\bin\bash.exe` — **that path does not exist**. The real binary is at **`C:\Program Files\Git\bin\bash.exe`** (verified present). Fix is `CLAUDE_CODE_GIT_BASH_PATH` in the `env` block of `C:\Users\ducat\.claude\settings.json`. Until that's set, Bash fails with `bash.exe not found` and **everything must run through PowerShell**.

**⚠️ Permission rules are Bash-only.** All ~50 rules in `settings.json` are scoped `Bash(...)`. There are **no `PowerShell(...)` rules**, so with Bash broken every command falls through to the auto-mode classifier — which blocks `git commit` and edits to `settings.json` itself. Symptom: "Blocked by classifier" on routine git work. Note `Bash(git commit *)` is already in the allow list; adding more Bash rules does nothing while Bash is broken.

**Orion cannot fix either of these.** Editing `settings.json` to widen its own permissions is blocked by design, and routing around that block would defeat its purpose. Nick makes those edits himself.

**PowerShell gotchas:**
- `Invoke-WebRequest` needs **`-UseBasicParsing`** or it fails with "PowerShell is in NonInteractive mode" (IE engine).
- Writing files with .NET `WriteAllLines` converts to **CRLF**; the memory repo is **LF**. Use `[System.IO.File]::WriteAllText` with `\n`, and always check `git diff --stat` looks proportional after a scripted edit.

**No Python installed.** Spreadsheet work runs on **Node + ExcelJS** from `C:\Users\ducat\Projects\project-budgets\` — that's where the deps live, `node` from anywhere else can't resolve `exceljs`.

See [[session-log]] for the sessions where each of these bit.

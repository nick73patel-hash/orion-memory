---
name: env-windows-machine
description: "Nick's Windows machine quirks — Bash FIXED 2026-08-29 (Git was never installed), PowerShell has no permission rules, no Python, where Node/ExcelJS lives"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 2ba035ee-bd06-45c1-bbbc-a749b8dffa53
  modified: 2026-09-05T17:03:07.806Z
---

Environment facts for Nick's Windows 11 machine that keep costing time when rediscovered.

**✅ Bash tool — FIXED 2026-08-29. Use Bash normally now.** Verified working: `uname -s` returns `MINGW64_NT-10.0-26200`, `git --version` 2.55.0.windows.3.

**The real root cause was NOT a path-resolution bug** (that was the earlier, wrong diagnosis — recorded here so it isn't re-derived). `CLAUDE_CODE_GIT_BASH_PATH` was set correctly and Bash *still* failed. The actual finding:

- `C:\Program Files\Git` was an **orphaned partial copy of a Git tree, never properly installed** — no uninstall entry in HKLM, WOW6432Node, or HKCU, and `winget list --id Git.Git` found nothing.
- `usr\bin` was **missing entirely** (only an empty `usr\share` survived), so **`msys-2.0.dll` did not exist anywhere** in the tree.
- `bin\bash.exe` and `sh.exe` were present but are ~47 KB stubs — they cannot run without the MSYS2 runtime in `usr\bin`. `git.exe` worked throughout because it's self-contained, which is why nothing else looked broken.

**The fix:** elevated `winget install --id Git.Git --exact --scope machine --silent` (official Git for Windows GitHub release, installer hash verified). It installs to `C:\Program Files\Git`, so the **existing `CLAUDE_CODE_GIT_BASH_PATH` value started working with no settings.json change**. `usr\bin` now holds 367 files incl. `msys-2.0.dll`. Needs UAC — Nick clicks Yes; `Start-Process powershell -Verb RunAs` from a non-elevated shell raises the prompt on his desktop.

**Diagnostic that settles it fast if Bash ever breaks again:** `Test-Path "C:\Program Files\Git\usr\bin\msys-2.0.dll"`. False = the install is gutted, reinstall. Don't chase the env var.

**⚠️ Permission rules are Bash-only.** All ~50 rules in `settings.json` are scoped `Bash(...)`; there are **no `PowerShell(...)` rules**. That's why, while Bash was broken, every command fell through to the auto-mode classifier and routine `git commit` got blocked. **With Bash working these rules apply again** — so run git through the Bash tool, not PowerShell, to stay inside them.

**Fallback if Bash breaks again — the Run-in-terminal button (proved 2026-08-29).** Put the command in a ```bash fence and let Nick click Run. That terminal spawns with a **stripped PATH — `git` is not on it** — so use the full path with PowerShell's `&` call operator:

`& "C:\Program Files\Git\cmd\git.exe" -C "<repo>" push`

**Orion still cannot edit `settings.json`.** Widening its own permissions is blocked by design, and routing around that block would defeat its purpose. Nick makes those edits himself.

**PowerShell gotchas** (still relevant when PowerShell is the right tool):
- `Invoke-WebRequest` needs **`-UseBasicParsing`** or it fails with "PowerShell is in NonInteractive mode" (IE engine).
- Writing files with .NET `WriteAllLines` converts to **CRLF**; the memory repo is **LF**. Use `[System.IO.File]::WriteAllText` with `\n`, and always check `git diff --stat` looks proportional after a scripted edit.

**🔒 GitHub auth = Git Credential Manager, NOT tokens in URLs (set 2026-08-29).** `credential.helper=manager` is set globally and holds a working GitHub credential in Windows Credential Manager (encrypted, per-user). **Never put a PAT in a remote URL** — three repos (`orion-memory`, `condo-assistant`, `perpetual-blue-crm`) each had one sitting in plaintext in `.git/config` for weeks; all three were stripped and verified to authenticate fine without it. Audit command: `find Projects .claude -maxdepth 4 -name config -path "*/.git/*" | xargs grep -lE 'https://[^@/]+@'` — any hit is a leak.

**⚠️ Orion's shell sees a DIFFERENT filesystem view than Nick's terminal — don't trust `Test-Path` about installs (learned 2026-08-30).** `Test-Path "$env:APPDATA\npm\vercel.cmd"` returned **True** in Orion's shell and **False** in Nick's real PowerShell, for the identical path and identical `$env:APPDATA`. Vercel was never actually installed. Orion insisted on a wrong answer three times off the back of it. **To check whether a tool exists, RUN it — don't stat a file.** Project files under `Projects\` are real and shared; `AppData` is not reliable. **`.env*` files are invisible to Orion entirely** (sandbox protection — good; it means secrets can't be read or leaked).

**PowerShell execution policy blocks `.ps1` shims.** `npx` resolves to `npx.ps1` and dies with *"running scripts is disabled on this system"*. **Use the `.cmd` form: `npx.cmd`, `npm.cmd`, `git.exe`.** This is a standing workaround, not a one-off.

**✅ Vercel CLI authenticated 2026-08-30** as `nick73patel-hash`, and `perpetual-blue-crm` is linked (repo-level: `.vercel/repo.json`). Orion can pull production runtime logs directly — `cmd //c "npx.cmd vercel ls perpetual-blue-crm"`, then `vercel logs <deployment-url>`. **Use this instead of rebuilding locally to reproduce a production error** (that cost 2.5 hours on 2026-08-29). Note the perpetualbluebvi.com *website* project is on Nick's son's account and won't appear here.

**No Python installed.** Spreadsheet work runs on **Node + ExcelJS** from `C:\Users\ducat\Projects\project-budgets\` — that's where the deps live, `node` from anywhere else can't resolve `exceljs`.

**🚫 NEVER use `sed` on anything containing a Windows path (learned 2026-09-04).** `sed 's|...|Projects\\condo-assistant|'` wrote a literal **`0x0F` control byte** into a memory file — GNU sed reads `\c` in the *replacement* as a control-character escape (`\co` = Ctrl-O). It corrupts **silently**: the file looks nearly right, and the failure only surfaced later when the Edit tool could not match the string. Two earlier attempts with different quoting failed the same way.

**Use Node `split`/`join` instead** — no escaping layer at all:
```js
s = s.split("Documents\\condo-assistant").join("Projects\\condo-assistant")
```
Same rule for any replacement containing backslashes. For multi-line content, write the text with the Write tool and have Node read it from a file rather than passing it through the shell — heredocs plus `node -e '...'` in one command also break on embedded quotes.

**💵 And NEVER pass prose containing `$` amounts through a double-quoted shell string (bit me 2026-09-05).** `node -e "...Henry ($3,000/mo)..."` — bash expanded `$3` as a positional parameter and wrote **`(,000/mo)`** into a memory file. Every dollar figure in the paragraph was silently destroyed; `$21,500` became `1,500`. Same silent-corruption class as the sed backslash trap. **Content with money, backslashes, or quotes goes through the Write tool, then Node reads the FILE.** Never inline it into a shell command. Audit after any scripted edit: `grep -n "(,[0-9]" <file>`.

**⚠️ Shell blocks handed to Nick must use `;` not `&&` (learned 2026-09-05).** The app Run button feeds a shell block straight to PowerShell, where `&&` is a hard parse error - *"The token && is not a valid statement separator in this version."* He hit it on a git push.

**PowerShell `$( )` breaks on a paren inside a quoted regex.** `$($x -match '[,)]')` is a parse error - the subexpression parser counts parens without respecting the string quoting. Assign to a variable first.

**Read the clipboard back before telling Nick what is in it.** `Get-Clipboard -Raw` answers "is this the right thing?" in one call. He asked twice in one session.

See [[session-log]] for the sessions where each of these bit.

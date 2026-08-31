---
name: env-supabase-sql-editor
description: Supabase SQL editor — the word INTO inside a string literal breaks the query; keep prose out of SQL values
metadata: 
  node_type: memory
  type: reference
  originSessionId: 95d1d1db-85af-4a17-ae21-ba340d6d9256
  modified: 2026-08-31T01:03:04.883Z
---

# Supabase SQL editor — the `INTO`-in-a-string trap

**Discovered 2026-08-30 after roughly an hour of dead ends.** Applying `migration_028` to the Perpetual Blue project failed repeatedly with:

```
ERROR: 42P01: relation "the" does not exist
```

**The SQL was valid.** It parsed clean through `pgsql-parser` (real libpg_query), every quote was balanced, there was no bare identifier anywhere, and all 122 lines were read by eye.

## What actually gave it away

Rewriting the offending memo text changed the error to match:

| Memo text in the string | Error |
|---|---|
| `... Cash INTO the company ...` | `relation "the" does not exist` |
| `... Cash into company ...` | `relation "company" does not exist` |

**The failing identifier is always the word immediately after `into`.** Postgres was reading `INTO` as the SQL keyword — i.e. the surrounding single quotes were not being honoured as string delimiters by the time the statement reached the server. Removing the prose fixed it instantly.

## The rule

**Keep SQL keywords — above all `INTO` — out of string literals when SQL will be pasted into the Supabase SQL editor.** Write terse values. Put the explanation in a `--` comment (comments were fine) or in the design doc, never inside a quoted value.

Note `check` is also a reserved word and cannot be used as a column alias (`AS check`) — use something else.

## What did NOT cause it (all ruled out with evidence, don't re-test)

- **Not file size.** Failed at 58 KB, at 13 KB and at 684 bytes alike.
- **Not truncation on paste.** Splitting into three parts and then into six single statements failed at exactly the same statement.
- **Not comments or non-ASCII.** A comment-stripped, ASCII-only version failed identically.
- **Not the `%` in `ILIKE '%mercury%'`.** That statement was the only one containing `%`, which looked damning — rewriting it as `strpos(lower(name),'mercury') > 0` changed nothing.
- **Not the clipboard.** Copying from Notepad by hand failed the same way.
- **Not a syntax error.** libpg_query accepted the file.

## The method that found it

**Bisect by statement.** Split the file so each statement can be run alone, then run them in order until one fails. Steps 1, 2, 3 and 5 passed; step 4 failed. From there it was one variable at a time until the error message itself changed — *the error message changing in step with the text was the actual clue*.

**Generalisable:** when an error names an identifier that does not appear anywhere in your source, suspect that a string literal is ending earlier than you think, and look at the word right before the named one.

Related: [[env-windows-machine]], [[project-perpetual-blue]].

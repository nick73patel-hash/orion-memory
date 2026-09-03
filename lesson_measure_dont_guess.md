---
name: lesson-measure-dont-guess
description: When a failure resists guessing, print the SHAPE of the invisible thing instead of theorising about it
metadata:
  type: feedback
---

When something opaque keeps failing, stop generating theories and **measure the thing you cannot see**.

**Why:** Twice now this has cost hours. On 2026-08-30 a Supabase SQL error (`relation "the" does not exist`) survived four wrong theories and four shipped fixes; a three-minute bisect to single statements found it immediately. On 2026-09-03 a `FATAL: password authentication failed` survived four workflow runs and a full password reset; a diagnostic step that printed the *shape* of the secret — total length, username, password length, password symbol count, host, whitespace check, **without ever printing the secret** — made the cause arithmetic-obvious in one run (Supabase's `[YOUR-PASSWORD]` template brackets had been left around the password: 17 chars/3 symbols where ~15 alphanumeric was expected).

**How to apply:** The moment a second theory is about to be shipped without evidence, build the instrument instead. For secrets, print length, character-class counts, and delimiters — never the value. For SQL, bisect to single statements. For CI, add a step that reports state before the failing step. The instrument almost always costs less than the guess it replaces.

Related: [[env-supabase-sql-editor]], [[pb-db-backup]], [[pref-look-before-done]]

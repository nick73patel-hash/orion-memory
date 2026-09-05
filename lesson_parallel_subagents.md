---
name: lesson-parallel-subagents
description: "How to run many subagents in ONE repo without them colliding — pre-assigned migration numbers, file ownership, and no git"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a4adeb55-6c9b-466d-95b6-ac93c8c3f185
  modified: 2026-09-05T15:44:50.239Z
---

When Nick says "assign to as many subagents as possible", parallelism only pays if collisions are prevented **before dispatch**. Proven on 2026-09-04: 5 agents, ~10–12 hours of sequential work, done in well under an hour with zero conflicts.

**Why:** agents left to their own devices each pick the same next migration number, each edit the same shared nav file, and each run `git add`/`commit` into one working tree — which corrupts the index. The speed-up is real, but only with a contract set up front.

**How to apply — do all five, in the dispatch prompt:**

1. **Pre-assign migration numbers explicitly** (031 to A, 032 to B, …). Otherwise three agents each create `migration_031_*.sql`.
2. **Assign explicit file ownership**, and *name the files each agent must NOT touch*. List the other agents' files by path.
3. ⭐ **FORBID agents from running any git command.** They leave changes uncommitted; the orchestrator commits once at the end. This is the single most important rule.
4. **Withhold shared files from every agent** and do them yourself at integration — nav (`Sidebar.tsx`), role/module lists (`lib/roles.ts`), anything every feature touches.
5. **Fix the schema contract up front** and give it to the dependent agent verbatim, so it builds against the contract instead of waiting for the migrations to land. *This is where the real time saving comes from.*

Also tell each agent to **distinguish its own build errors from another agent's in-flight files**, or you get false failure reports.

**Two things that showed up in practice:**
- **An agent that hits a boundary should report, not guess.** Agent D correctly refused to write a migration it had been told was off-limits. That constraint was mine, so finishing the job was mine too — not the agent's failure.
- **Verify a stalled agent's claims rather than inheriting them.** One agent hung for 600s chasing a CRLF problem that did not exist (the file had 0 CR bytes).

Related: [[pref-delegate-subagents]], [[pref-look-before-done]]

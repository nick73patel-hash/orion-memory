---
name: pb-db-backup
description: Perpetual Blue CRM nightly encrypted Supabase backup — where it lives, how it works, and the one key that can open it
metadata:
  type: project
---

The Perpetual Blue CRM database backs itself up nightly to GitHub. **Live and verified 2026-09-03** (workflow run #42).

- **Workflow:** `.github/workflows/db-backup.yml` in `nick73patel-hash/perpetual-blue-crm`
- **Schedule:** 08:00 UTC daily, plus manual `workflow_dispatch`
- **Output:** `backups/perpetual-blue-<YYYY-MM-DD>.sql.gz.gpg` — `pg_dump` → `gzip` → `gpg --symmetric --cipher-algo AES256`
- **Retention:** 30 days, pruned by the **date in the filename**, never by mtime (`actions/checkout` rewrites every mtime each run)
- **Connection:** Session pooler `aws-1-us-west-2.pooler.supabase.com:5432`, user `postgres.udmndiuasxgglqvskxua`. The Direct connection is IPv6-only and unreachable from GitHub runners.
- **Client:** must call `/usr/lib/postgresql/17/bin/pg_dump` explicitly — the bare `pg_dump` wrapper resolves to the runner's preinstalled client 16, which refuses to dump the 17.6 server.

**⚠️ The single point of failure: `BACKUP_ENCRYPTION_KEY`.** It is a GitHub secret, therefore write-only — neither Nick nor Orion can read it back. It is the only thing that can decrypt any of these files. If it is lost, every backup is permanently unreadable. It must be stored somewhere that is **neither GitHub nor the laptop**. As of 2026-09-03 this was flagged to Nick but **not confirmed done** — worth asking again.

**Pushing changes to this file requires a PAT with `workflow` scope.** The current PAT lacks it, so edits to anything under `.github/workflows/` are rejected by `git push` and must go through the GitHub web editor. See [[env-windows-machine]].

Related: [[project-perpetual-blue]], [[pb-licenses]]

## Open gaps (as of 2026-09-03)

1. **Never restore-tested.** No one has decrypted a snapshot. Test: `gpg --decrypt backups/<file> | gunzip | head -30` run in Nick's own terminal so pinentry prompts him (never via Orion's Bash tool — it blocks on the dialog).
2. **⚠️ Supabase Storage is NOT backed up.** Four buckets in use — `pb-receipts`, `pb-documents`, `pb-capital`, `avatars`. `pg_dump` captures only the metadata rows; the actual uploaded files (receipt photos, documents) exist nowhere else. A restore today would produce dead links for every receipt.
3. **The cron has never fired successfully** — every green run so far was `workflow_dispatch`. First unattended proof is 08:00 UTC.
4. **30-day retention blind spot** — corruption discovered on day 31 exists in every retained snapshot. A never-pruned monthly archive would cover it.
5. **`backups/README.md` is stale** — still describes `find -mtime +30` pruning, which never matched anything.
6. **No deploy/config capture** — Vercel environment variables are not recorded anywhere outside Vercel.
7. **Whether `auth.users` is in the dump is unverified** — if it is not, a restore yields data with no logins. The restore test in (1) answers this.

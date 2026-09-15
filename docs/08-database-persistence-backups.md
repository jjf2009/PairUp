# 08 — Database Persistence & Backups

## Goal
Run Postgres in production with proper migrations, automated backups, and
a backup you've actually proven you can restore.

## Why it matters for job-readiness
"We had backups" and "we tested restoring the backup" are very different
claims. Data loss is one of the most career-damaging incidents in this
field — this module is about building the habit of proving recoverability,
not just enabling a cron job and hoping.

## Prerequisites
- Module 03 (Postgres running in compose).
- Module 07 (production traffic actually hitting the app, generating real data).

## Concepts to learn
- Named volumes vs bind mounts for database data specifically — why you
  want a real volume, not a bind mount, for Postgres data directories.
- Migrations: schema versioning tools (Prisma Migrate, Knex, Alembic,
  whatever fits your stack) vs hand-run SQL.
- `pg_dump`/`pg_restore` basics.
- Backup strategy: frequency, retention, where backups are stored (never
  only on the same VPS as the database — see below).
- Point-in-time recovery concept (even if you don't implement WAL
  archiving yet, know what it is and when you'd need it).

## Hands-on: apply to PairUp
1. Adopt a migrations tool; convert your module-03 ad-hoc schema into
   tracked migration files. Run migrations as an explicit CD step
   (module 05), not automatically on every container start.
2. Write a backup script that runs `pg_dump`, compresses the output, and
   uploads it off the VPS (S3-compatible storage — Backblaze B2, DO Spaces).
3. Schedule it with a `systemd` timer or cron on the VPS (or as a
   scheduled GitHub Actions workflow that SSHes in and triggers it).
4. Set a retention policy (e.g. keep daily backups for 14 days) and script
   the cleanup of old ones.
5. **Prove it works**: spin up a throwaway Postgres container, restore the
   latest backup into it, and verify the data matches production. Do this
   for real, not hypothetically.

## Definition of done
- [ ] Schema changes go through tracked migrations, applied as an explicit
      deploy step.
- [ ] A backup runs automatically on a schedule and lands in off-VPS storage.
- [ ] You have restored a real backup into a fresh database at least once
      and confirmed the data is correct.

## Stretch goals
- Add alerting (ties into module 11) if a scheduled backup job fails or
  doesn't run.
- Look into WAL archiving / point-in-time recovery if you want to go
  beyond daily-snapshot backups.

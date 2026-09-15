# 03 — Docker Compose (Multi-Service)

## Goal
Run PairUp's backend, Postgres, and Redis together as one local stack
defined entirely in a `docker-compose.yml`.

## Why it matters for job-readiness
Real apps are never one container. Compose is how you learn multi-service
orchestration concepts (service discovery, dependency ordering, shared
networks/volumes) at a scale simple enough to actually understand, before
Kubernetes (module 10) throws the same concepts at you with 10x the YAML.

## Prerequisites
- Module 02 (you need a working Dockerfile for the backend).

## Concepts to learn
- Compose file structure: `services`, `networks`, `volumes`.
- Service discovery by service name (containers reach Postgres at
  `postgres:5432`, not `localhost`).
- `depends_on` and why it doesn't wait for "ready", just "started"
  (healthchecks fix this).
- Healthchecks (`healthcheck:` block) and `depends_on: condition: service_healthy`.
- Named volumes for persistent data vs anonymous/bind mounts.
- Environment variables via `.env` file + `environment:`/`env_file:`.
- `docker compose up/down/logs/exec/ps`.

## Hands-on: apply to PairUp
1. Add `postgres` and `redis` services to a `docker-compose.yml`, each
   with a named volume for persistence.
2. Add healthchecks to both (`pg_isready` for Postgres, `redis-cli ping`
   for Redis).
3. Add your backend service (built from the module 02 Dockerfile) with
   `depends_on` using `condition: service_healthy` for both dependencies.
4. Wire real connection strings via environment variables — backend reads
   `DATABASE_URL`/`REDIS_URL` from env, not hardcoded values.
5. Add a minimal migration (even just a `users` or `sessions` table) that
   runs on backend startup, and confirm data survives `docker compose down`
   + `docker compose up` (but not `docker compose down -v`).
6. Use `docker compose exec backend sh` to shell into the running
   container and `docker compose logs -f` to tail all services at once.

## Definition of done
- [ ] `docker compose up` brings up all three services with one command.
- [ ] Backend only starts accepting traffic after Postgres/Redis report healthy.
- [ ] Data in Postgres survives a stop/start cycle.
- [ ] No secrets or connection strings are hardcoded in application code —
      all come from environment variables (full secrets hygiene comes in
      module 09, but start the habit now).

## Stretch goals
- Split into `docker-compose.yml` + `docker-compose.override.yml` for
  local-dev-only overrides (hot reload, exposed DB port for a GUI client).
- Add a `pgadmin` or `redis-commander` service for local debugging, gated
  behind a compose profile so it's not always running.

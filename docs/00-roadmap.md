# PairUp DevOps Roadmap

This is a learning roadmap, not just a build plan. The goal isn't "ship
PairUp fast" — it's **become job-ready in DevOps** by building a real
product feature-by-feature, and letting each feature force you to learn one
DevOps discipline properly instead of skimming a tutorial.

Every module lives in this folder as `NN-topic.md`. Work through them in
order — each one assumes the infra/app state left behind by the previous
one. Don't skip ahead even if a later topic looks more exciting; the whole
point is that module 12 (sandboxed execution) is *hard* precisely because
modules 1–11 gave you the container, orchestration, and security vocabulary
to reason about it.

## Setup

- **Deploy target:** a single self-hosted VPS (Hetzner CX22 / DigitalOcean
  droplet / similar — cheap is fine, ~2GB RAM minimum, 4GB+ once you reach
  k3s). Self-hosted on purpose: managed platforms (Vercel, Railway, ECS)
  hide exactly the skills you're trying to learn.
- **Skill level assumed:** beginner. Each module front-loads the concepts
  you need before asking you to touch anything.
- **Pace:** roughly 1 module every 1–2 weeks if you're doing this alongside
  other work. Don't rush modules 1–3 (Linux, Docker, Compose) — everything
  else builds on them.

## Module index

| # | Module | You'll be able to... |
|---|--------|----------------------|
| [01](01-linux-networking-fundamentals.md) | Linux & networking fundamentals | Provision and secure a bare VPS by hand |
| [02](02-docker-fundamentals.md) | Docker fundamentals | Containerize a service from a Dockerfile |
| [03](03-docker-compose-multi-service.md) | Docker Compose | Run app + Postgres + Redis together locally |
| [04](04-ci-github-actions.md) | CI with GitHub Actions | Auto-lint/test/build on every push |
| [05](05-cd-automated-deploys.md) | CD — automated deploys | Auto-deploy to the VPS on merge to main |
| [06](06-infrastructure-as-code-terraform.md) | Infrastructure as Code (Terraform) | Provision the VPS itself from code |
| [07](07-reverse-proxy-tls.md) | Reverse proxy & TLS | Serve PairUp over HTTPS on a real domain |
| [08](08-database-persistence-backups.md) | Database persistence & backups | Run Postgres in prod without losing data |
| [09](09-secrets-management.md) | Secrets management | Stop hardcoding credentials anywhere |
| [10](10-container-orchestration-k3s.md) | Container orchestration (k3s) | Run PairUp on a real (small) Kubernetes cluster |
| [11](11-monitoring-logging-observability.md) | Monitoring, logging & observability | See what your system is doing in prod |
| [12](12-sandboxed-execution-scaling.md) | Sandboxed execution & scaling | Run untrusted user code safely, at scale |
| [13](13-webrtc-infra-turn-server.md) | WebRTC infra (TURN/STUN) | Make video calls work behind NATs/firewalls |
| [14](14-security-hardening.md) | Security hardening | Lock down the whole stack |
| [15](15-capstone-production-runbook.md) | Capstone: production runbook | Operate this like a real on-call engineer |

## How each module is structured

Every module file has the same sections:

1. **Goal** — what you can do after finishing it.
2. **Why it matters for job-readiness** — the real-world role/skill this maps to.
3. **Prerequisites** — modules/tools you need first.
4. **Concepts to learn** — the theory to study before touching a terminal.
5. **Hands-on: apply to PairUp** — concrete steps that build the actual project.
6. **Definition of done** — a checklist to know you're actually finished.
7. **Stretch goals** — optional, for going deeper on that topic.

## Overall progress checklist

- [ ] 01 — Linux & networking fundamentals
- [ ] 02 — Docker fundamentals
- [ ] 03 — Docker Compose (multi-service)
- [ ] 04 — CI with GitHub Actions
- [ ] 05 — CD — automated deploys
- [ ] 06 — Infrastructure as Code (Terraform)
- [ ] 07 — Reverse proxy & TLS
- [ ] 08 — Database persistence & backups
- [ ] 09 — Secrets management
- [ ] 10 — Container orchestration (k3s)
- [ ] 11 — Monitoring, logging & observability
- [ ] 12 — Sandboxed execution & scaling
- [ ] 13 — WebRTC infra (TURN/STUN)
- [ ] 14 — Security hardening
- [ ] 15 — Capstone: production runbook

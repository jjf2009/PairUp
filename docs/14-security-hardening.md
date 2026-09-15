# 14 — Security Hardening

## Goal
Go back over the whole stack — VPS, containers, CI, cluster — and close
the gaps that "get it working" left open.

## Why it matters for job-readiness
Security isn't a module you bolt on once; it's a pass you do deliberately
after the system works, and revisit continuously. This module is that
deliberate pass, and it maps directly to real security-review checklists
you'll be asked to run in a job.

## Prerequisites
- Modules 01–13 — this is a hardening pass across everything you built.

## Concepts to learn
- SSH hardening beyond module 01: `fail2ban` for brute-force protection,
  optionally moving off port 22, disabling unused auth methods.
- Container image scanning (Trivy) for known CVEs in your base images and
  dependencies, wired into CI so it runs on every build, not manually.
- Least privilege everywhere: non-root container users (module 02
  stretch goal, make it mandatory now), Kubernetes Pod
  `securityContext` (`runAsNonRoot`, dropped capabilities, read-only
  root filesystem where possible).
- Network policies in k3s: default-deny pod-to-pod traffic, only allow
  what's actually needed (the module-12 worker pool talking to Redis, but
  nothing talking to it except the API).
- Dependency scanning (`npm audit`/`dependabot` or equivalent) in CI.
- Rate limiting and basic abuse protection at the reverse proxy/ingress
  layer (relevant especially for the module-12 execution endpoint —
  it's the part of PairUp most attractive to abuse).
- The concept of a security review / threat model: for each component,
  ask "what's the worst thing an attacker reaching this could do?"

## Hands-on: apply to PairUp
1. Install and configure `fail2ban` on the VPS for SSH.
2. Add Trivy image scanning as a CI job (module 04's pipeline); fail the
   build on high/critical CVEs, or at minimum surface them visibly.
3. Audit every Dockerfile: confirm each runs as a non-root `USER`.
4. Add `securityContext` to every k3s Deployment: `runAsNonRoot: true`,
   drop all capabilities except what's strictly needed, read-only root
   filesystem where the app allows it.
5. Write a NetworkPolicy that default-denies pod-to-pod traffic in the
   cluster, then explicitly allow only the connections that must exist
   (API → Postgres, API → Redis, worker → Redis, worker → nothing else).
6. Add dependency scanning to CI (`npm audit --audit-level=high` or
   Dependabot alerts enabled on the repo).
7. Add rate limiting at the ingress/proxy for the code-execution endpoint
   specifically — it's a target for abuse (crypto-mining via free compute,
   spam submissions).
8. Do one deliberate threat-model pass over module 12 specifically: write
   down, in a doc or just out loud, every way you can think of that a
   malicious user could break out of or abuse the sandbox, and confirm
   each is actually mitigated.

## Definition of done
- [ ] `fail2ban` is active and you've confirmed it blocks repeated failed
      SSH attempts.
- [ ] CI fails (or clearly flags) on high/critical image vulnerabilities.
- [ ] Every container/Pod runs as non-root with minimal capabilities.
- [ ] A default-deny NetworkPolicy is in place with explicit allows only
      for real required traffic.
- [ ] The code-execution endpoint has rate limiting in front of it.

## Stretch goals
- Run a basic external port scan (`nmap`) against your own VPS and confirm
  only the ports you expect (80/443, and TURN's ports) are actually open.
- Set up automated dependency update PRs (Dependabot/Renovate) so this
  stays current instead of becoming stale after this module.

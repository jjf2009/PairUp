# 05 — CD: Automated Deploys

## Goal
A merge to `main` automatically ends up running on the VPS, with no manual
SSH-and-pull step.

## Why it matters for job-readiness
CI without CD is half the pipeline. This module is where "I can deploy
software" stops meaning "I ran a command on my laptop" and starts meaning
"I trust a pipeline to put code in front of users."

## Prerequisites
- Module 01 (VPS with SSH access).
- Module 03 (docker-compose stack).
- Module 04 (CI producing tagged images in GHCR).

## Concepts to learn
- Push-based deploys (CI SSHes into the server and pulls/restarts) vs
  pull-based (server polls/webhooks for new images) — you'll implement
  push-based here; pull-based (GitOps) is a stretch goal in module 10.
- Storing an SSH deploy key safely as a CI secret (deeper dive module 09).
- Idempotent deploy scripts (`docker compose pull && docker compose up -d`
  is safe to run repeatedly).
- Rollback strategy: keeping the previous image tag around so you can
  redeploy it if the new one is broken.
- Deploy notifications (optional): knowing a deploy happened without
  checking manually.

## Hands-on: apply to PairUp
1. Generate a dedicated deploy-only SSH key pair; add the public key to
   the VPS's `authorized_keys` for a low-privilege deploy user (not your
   personal admin user).
2. Store the private key as a GitHub Actions secret.
3. Extend the module 04 "push to main" workflow with a deploy job that:
   - SSHes into the VPS.
   - Pulls the newly built image tag.
   - Runs `docker compose up -d` to recreate only the changed service.
4. Add a simple rollback path: tag the previous production image as
   `previous` before deploying `latest`, so you can manually redeploy
   `previous` if something breaks.
5. Deliberately deploy a broken change (e.g. crash on startup), watch it
   fail in prod, and practice rolling back using the tag from step 4.

## Definition of done
- [ ] Merging to `main` results in the new version running on the VPS
      within a few minutes, with zero manual steps.
- [ ] You can roll back to the previous version in under 2 minutes.
- [ ] The deploy SSH key is scoped to a low-privilege user, not your
      personal login.

## Stretch goals
- Add a Slack/Discord webhook notification on deploy success/failure.
- Add a smoke test step after deploy (`curl` the `/health` endpoint) that
  fails the workflow (and ideally auto-rolls-back) if the new version
  doesn't come up healthy.

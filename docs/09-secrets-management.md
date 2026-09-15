# 09 — Secrets Management

## Goal
Eliminate every hardcoded credential in the project (app code, compose
files, Terraform, CI) and replace them with a real secrets-handling
pattern you can explain.

## Why it matters for job-readiness
Leaked secrets are one of the most common real-world security incidents.
Knowing how to keep secrets out of git, out of logs, and out of images —
and how to rotate them — is expected baseline knowledge, not an advanced
topic.

## Prerequisites
- Modules 04–06 (you now have secrets scattered across CI, compose env
  files, and Terraform variables — this module consolidates them).

## Concepts to learn
- Why secrets never belong in git, even in "private" repos (history
  persists, access changes over time, mistakes happen).
- `.env` files: fine for local dev, gitignored, never committed.
- GitHub Actions encrypted secrets — how they're masked in logs, their
  limitations (still visible to anyone who can edit workflows).
- Encrypting secrets *at rest* in the repo with SOPS + age (or git-crypt)
  for things like Terraform variable files you do want versioned.
- The concept of a dedicated secrets manager (HashiCorp Vault, or a
  hosted equivalent) — you don't need to run one for a solo project, but
  you should know what problem it solves that `.env` files don't
  (dynamic secrets, audit logs, fine-grained access, rotation).
- Secret rotation: what it means to rotate a leaked/expiring credential
  without downtime.

## Hands-on: apply to PairUp
1. Audit the whole repo: `grep` for anything that looks like a credential,
   API key, or connection string in tracked files. Fix every hit.
2. Confirm `.env`, `terraform.tfvars`, and any key files are in
   `.gitignore` (and were never committed — check with `git log -- <file>`).
3. Move all CI-needed secrets (deploy SSH key, registry credentials,
   Terraform provider token) into GitHub Actions encrypted secrets.
4. Set up SOPS + age for one real case: encrypt a Terraform `.tfvars` file
   containing the DNS/API token so it *can* be committed encrypted, and
   decrypt it only in CI.
5. Rotate at least one real credential (e.g. regenerate the deploy SSH
   key) end-to-end: generate new, update everywhere it's used, revoke the
   old one, confirm deploys still work.

## Definition of done
- [ ] `git log -p` across the whole repo history turns up zero real secrets
      (if it does, treat those as compromised and rotate them — see stretch).
- [ ] Every secret the app/CI/Terraform needs comes from an environment
      variable or encrypted-at-rest file, never a literal in tracked code.
- [ ] You've rotated at least one credential successfully with no downtime.

## Stretch goals
- If you ever find a secret was committed (even in an old commit), treat
  it as leaked: rotate it immediately, and separately learn how
  `git filter-repo`/BFG can scrub history (rotating matters more than
  scrubbing — a scrubbed-but-unrotated secret is still compromised).
- Try running HashiCorp Vault in dev mode locally just to see dynamic
  secret issuance in action, even if you don't productionize it.

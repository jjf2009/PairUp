# 04 — CI with GitHub Actions

## Goal
Every push and PR automatically lints, tests, and builds a Docker image —
and pushes it to a registry on merges to `main`.

## Why it matters for job-readiness
CI pipelines are table stakes. Being able to read and write a YAML
workflow, debug a failing CI run, and understand caching/matrix builds is
something you'll be asked about (or handed) on day one of almost any
DevOps/platform role.

## Prerequisites
- Modules 02–03 (something to lint/test/build).
- A GitHub repo (this one) with Actions enabled.

## Concepts to learn
- GitHub Actions structure: workflows, jobs, steps, triggers (`on: push`,
  `on: pull_request`).
- Runners (GitHub-hosted vs self-hosted — just the concept for now).
- Caching dependencies (`actions/cache`, or language-specific cache in
  `actions/setup-node`) to speed up repeat runs.
- Job dependencies (`needs:`) and running jobs in parallel vs sequence.
- Build args/secrets in workflows (`secrets.` context) — deeper dive in
  module 09.
- `docker/build-push-action` and authenticating to GHCR from a workflow.
- Status checks / branch protection tied to CI passing.

## Hands-on: apply to PairUp
1. Add `.github/workflows/ci.yml` triggered on push and PR to `main`.
2. Job 1: install deps, run linter, run tests (even a trivial one — the
   point is the pipeline, not test coverage yet).
3. Job 2 (`needs: [lint-test]`): build the Docker image from module 02's
   Dockerfile to confirm it still builds — fail loud if it doesn't.
4. Add a separate job/workflow that, only on push to `main`, builds and
   pushes the image to GHCR tagged with the git SHA and `latest`.
5. Turn on branch protection on `main` requiring the CI workflow to pass
   before merge.
6. Deliberately break something (failing test, bad Dockerfile line), open
   a PR, watch CI fail, read the logs, fix it.

## Definition of done
- [ ] Every PR shows CI status checks and blocks merge if they fail.
- [ ] Merges to `main` produce a new image in GHCR tagged with the commit SHA.
- [ ] You've debugged at least one real CI failure end-to-end using the
      Actions log viewer, not just guessing.

## Stretch goals
- Add a matrix build (e.g. two Node versions) to see matrix syntax in practice.
- Add a cache step and measure the before/after CI run time.
- Add a status badge to `README.md`.

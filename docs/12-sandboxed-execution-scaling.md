# 12 — Sandboxed Execution & Scaling

## Goal
Build the piece that makes PairUp actually PairUp: a job-queue-backed
engine that runs arbitrary user-submitted code safely, and scales its
workers under load.

## Why it matters for job-readiness
This is the hardest and most differentiating module — "safely run
untrusted code" is a real system-design interview question, and you'll
have shipped an actual answer to it. It combines queueing, isolation, and
autoscaling: three separate skills most tutorials teach in isolation.

## Prerequisites
- Modules 02–03 (containers, Redis available).
- Module 10 (you'll run the worker pool as its own scalable Deployment).
- Module 11 (you'll want metrics on queue depth/execution time from day one).

## Concepts to learn
- Why you can never `exec()` or run user code directly in your app
  process — the isolation boundary has to be a separate, disposable
  execution environment.
- Job queues: producer/consumer pattern, Redis + BullMQ (or similar) —
  API enqueues a job, workers dequeue and execute.
- Container-based isolation for untrusted code: running each submission
  in a fresh, short-lived container with no network access, strict
  CPU/memory/time limits, and a read-only filesystem where possible.
- Stronger isolation options beyond a plain container (gVisor, Firecracker
  microVMs) — know what problem they solve (kernel-level attack surface)
  even if you start with plain Docker limits.
- Autoscaling workers: scaling the worker pool based on queue depth, not
  just CPU (Kubernetes HPA supports custom metrics for exactly this).
- Timeouts and killing runaway jobs — what happens to a job that infinite-loops.

## Hands-on: apply to PairUp
1. Add an execution API endpoint: accepts code + language, enqueues a job
   in Redis/BullMQ, returns a job ID immediately (never executes inline).
2. Build a worker service, separate from the API, that dequeues jobs and
   runs the code inside a throwaway Docker container per job:
   - `--network none` (no network access from user code).
   - CPU/memory limits (`--cpus`, `--memory`).
   - A hard wall-clock timeout that kills the container if exceeded.
   - The container is removed immediately after (`--rm`), win or lose.
3. Deploy the worker as its own k3s Deployment, separate from the API
   Deployment, so it scales independently.
4. Expose queue depth as a Prometheus metric (ties to module 11) and set
   up a Horizontal Pod Autoscaler on the worker Deployment driven by that
   metric (via a custom metrics adapter, or a simpler polling-based
   scaler if the custom-metrics path proves too heavy for a first pass).
5. Load-test it: submit a burst of jobs (including at least one
   deliberately malicious/runaway one — infinite loop, fork bomb, network
   scan attempt) and confirm: the app stays up, the runaway job gets
   killed by the timeout, and no job can reach the network or other jobs'
   containers.

## Definition of done
- [ ] User code never runs in the API process — always in an isolated,
      disposable container via the job queue.
- [ ] CPU, memory, network, and time limits are all enforced and you've
      tested each one actually triggers (not just configured and assumed).
- [ ] The worker pool scales up under queue-depth load and back down when
      idle.
- [ ] You can explain, precisely, what stops a submitted script from
      reading another user's job data or reaching the internet.

## Stretch goals
- Swap plain Docker isolation for gVisor (`runsc` runtime) and compare.
- Add per-language resource profiles (e.g. compiled languages get a
  longer timeout than interpreted ones).
- Persist execution results/logs and expose them back through the API for
  the AI feedback report feature to consume later.

# 15 — Capstone: Production Runbook

## Goal
Operate PairUp the way a real team would: with a documented deploy
process, an incident/rollback runbook, a load test you've actually run,
and a clear story of what you learned to tell in an interview.

## Why it matters for job-readiness
This module has no new infrastructure — it's where you prove the previous
14 modules add up to something you can *operate*, not just something that
once worked when you built it. This is also the module that turns into
your resume bullet points and interview talking points.

## Prerequisites
- Modules 01–14, all functioning together.

## Concepts to learn
- What a runbook is and why teams write them before an incident, not
  during one.
- Load testing basics: what you're measuring (throughput, latency
  percentiles, error rate under load) and common tools (`k6`, `locust`,
  `autocannon`).
- Incident response basics: detect → mitigate → resolve → postmortem, and
  why "who's to blame" isn't a postmortem question.
- Capacity planning at a small scale: knowing roughly how many concurrent
  users/sessions/executions your current VPS size can handle before you'd
  need to scale up or out.

## Hands-on: apply to PairUp
1. Write `docs/runbook.md` covering:
   - How to deploy a change (should just be "merge to main" thanks to
     module 05 — document what to check if it doesn't go smoothly).
   - How to roll back a bad deploy.
   - How to check system health (link to the module-11 Grafana dashboard).
   - What to do if the code-execution worker pool falls behind (queue
     depth alert from module 11/12).
   - How to rotate a credential (module 09) if one leaks.
2. Run a real load test against the deployed system (not localhost) using
   `k6` or similar: ramp up concurrent users hitting the editor/API and
   submitting code executions, and record throughput/latency/error-rate
   results.
3. Deliberately trigger one incident end-to-end and write the postmortem:
   e.g. kill the database container mid-traffic, or exhaust the worker
   pool with a burst of jobs. Record what broke, what your monitoring
   showed, how you fixed it, and what you'd change to prevent it.
4. Based on the load test, write down (in the runbook or a short doc)
   your current system's approximate capacity and what the first
   bottleneck would be if traffic grew 10x — and which module's tooling
   you'd lean on to fix it (bigger VPS via module 06? more worker
   replicas via module 10/12? read replica via module 08?).
5. Write a short project retrospective: which module was hardest, what
   you'd do differently starting over, and a 2-3 sentence summary of this
   project suitable for a resume/LinkedIn "Projects" section.

## Definition of done
- [ ] `docs/runbook.md` exists and is detailed enough that someone who
      didn't build PairUp could follow it during an incident.
- [ ] You've run and recorded a real load test against the deployed system.
- [ ] You've run one real (deliberately triggered) incident through
      detection → mitigation → postmortem, with a written postmortem.
- [ ] You can state your system's approximate current capacity and its
      first bottleneck under 10x load, and back it up with the load test data.

## Stretch goals
- Turn this whole roadmap + runbook + postmortem into a short blog post or
  portfolio write-up — this is exactly the kind of artifact that stands
  out in a DevOps job application.
- Repeat the load test after making one concrete scaling improvement
  (e.g. add a second worker replica) and compare before/after numbers.

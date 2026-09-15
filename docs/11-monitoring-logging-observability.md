# 11 — Monitoring, Logging & Observability

## Goal
See what PairUp is actually doing in production: metrics, logs, and
alerts, instead of finding out something broke from a user complaint.

## Why it matters for job-readiness
"It's deployed" isn't the job — "I know when it's unhealthy before the
user tells me" is. Observability is a named skill on almost every SRE/
DevOps job description, and this module gives you a real (small) version
of a production monitoring stack.

## Prerequisites
- Module 10 (monitoring a k3s cluster is the realistic target here,
  though the concepts apply equally if you stayed on compose).

## Concepts to learn
- The three pillars: metrics, logs, traces (traces are a stretch goal
  here — full tracing is a lot for a solo beginner project).
- Prometheus: pull-based metrics scraping, what a `/metrics` endpoint is,
  PromQL basics.
- Grafana: dashboards built on top of Prometheus (and/or Loki) data sources.
- Loki (or simpler: just centralizing container logs) for log aggregation
  — why `docker logs`/`kubectl logs` on individual containers doesn't
  scale past one node.
- Health checks vs liveness/readiness probes (k8s-specific: the difference
  between "restart this" and "stop sending it traffic").
- Alerting: Alertmanager basics, or even a simple webhook-based alert —
  the point is *actionable* alerts, not just dashboards nobody watches.

## Hands-on: apply to PairUp
1. Add a `/metrics` endpoint to the backend (basic request count/latency
   histogram is enough to start).
2. Deploy Prometheus + Grafana into the k3s cluster (or via compose if you
   kept that path), scraping the backend and node-level metrics.
3. Build one real Grafana dashboard: request rate, error rate, latency,
   and basic host metrics (CPU/memory/disk).
4. Add liveness and readiness probes to the backend Deployment; deliberately
   break the readiness check and watch k8s stop routing traffic to that pod.
5. Set up log aggregation (Loki + Promtail, or at minimum shipping
   container logs somewhere queryable) so you're not SSHing in to `docker
   logs`/`kubectl logs` every time.
6. Configure one real alert (e.g. error rate above a threshold, or the
   backup job from module 08 failing) that notifies you outside of
   Grafana (email/webhook).

## Definition of done
- [ ] You have a Grafana dashboard showing real request rate, error rate,
      and latency for PairUp, plus host resource metrics.
- [ ] Liveness/readiness probes are configured and you've verified they
      actually change routing/restart behavior.
- [ ] Logs from all services are queryable in one place without SSHing
      into the VPS.
- [ ] At least one alert has fired for real (even a manufactured failure)
      and reached you outside the dashboard.

## Stretch goals
- Add basic distributed tracing (OpenTelemetry) once you have more than
  one backend service talking to each other.
- Define SLOs (e.g. "99% of requests under 300ms") and build a dashboard
  panel that tracks error budget against them.

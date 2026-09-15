# 10 — Container Orchestration with k3s

## Goal
Migrate PairUp from docker-compose to a real (lightweight) Kubernetes
cluster running on your VPS, using k3s.

## Why it matters for job-readiness
Kubernetes is the most commonly requested orchestration skill in DevOps
job postings. k3s gives you the real k8s API on a single cheap VPS, so
you learn the concepts (Deployments, Services, Ingress, ConfigMaps) without
needing a multi-node cluster or a cloud bill.

## Prerequisites
- Modules 02–03 (you understand containers and multi-service composition —
  k3s is "compose's concepts, formalized and distributed").
- Module 07 (you understand what a reverse proxy/ingress needs to do).

## Concepts to learn
- Core objects: Pod, Deployment, Service, ConfigMap, Secret, Ingress.
- Why Pods aren't containers (a Pod can hold multiple containers sharing
  network/storage) and why you rarely create Pods directly (Deployments
  manage them).
- Services: ClusterIP vs NodePort vs LoadBalancer, and what each means on
  a single-node cluster.
- Ingress controllers (k3s ships with Traefik by default) and how Ingress
  resources replace your module-07 reverse-proxy config.
- `kubectl` basics: `get`, `describe`, `logs`, `exec`, `apply -f`.
- Declarative manifests (YAML you `apply`) vs imperative `kubectl` commands.
- Rolling updates and how a Deployment achieves zero-downtime deploys by
  default (contrast with module 05's compose-based restart).

## Hands-on: apply to PairUp
1. Install k3s on the VPS (single-node cluster is fine).
2. Convert the backend service into a Deployment + Service manifest.
   Convert Postgres/Redis similarly, or intentionally keep them as
   compose-managed "external" services for now if you want to isolate
   the learning curve — decide and document why.
3. Move config (non-secret env vars) into a ConfigMap, secrets into a
   Kubernetes Secret (built from files created via module 09's process,
   never typed directly into a manifest that gets committed).
4. Replace the module-07 Caddy/Nginx compose service with an Ingress
   resource routing to the backend Service.
5. Update the module-05 CD workflow to `kubectl apply` the manifests (or
   `kubectl set image` for the new tag) instead of `docker compose up -d`.
6. Trigger a rolling update deliberately and watch `kubectl rollout status`
   — confirm there's no downtime during the update.
7. Kill a pod manually (`kubectl delete pod ...`) and watch the Deployment
   recreate it automatically — this is the core value prop of k8s over
   plain Docker.

## Definition of done
- [ ] PairUp's backend runs as a k3s Deployment with a Service and Ingress,
      reachable at your real HTTPS domain.
- [ ] Config and secrets are separated into ConfigMap/Secret objects, not
      baked into images or manifests in plaintext.
- [ ] A deploy via CI results in a rolling update with zero downtime.
- [ ] You can explain what happens, step by step, when you delete a Pod
      that belongs to a Deployment.

## Stretch goals
- Add resource requests/limits to the Deployment and observe what happens
  if you set them too low (OOMKilled, throttling).
- Try a Horizontal Pod Autoscaler against a simple CPU-based metric.
- Look into GitOps (ArgoCD/Flux) as the "pull-based deploy" alternative to
  module 05's push-based CD — you don't need to adopt it, just understand
  the tradeoff.

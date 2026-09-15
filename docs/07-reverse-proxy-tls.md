# 07 — Reverse Proxy & TLS

## Goal
Serve PairUp over HTTPS on your real domain, with a reverse proxy in
front of the app containers instead of exposing them directly.

## Why it matters for job-readiness
Almost nothing in production talks directly to the internet — there's
always a proxy/load balancer in front. Understanding TLS termination,
virtual hosts, and why you never expose an app container's port directly
is fundamental production-networking knowledge.

## Prerequisites
- Module 03 (compose stack to sit behind the proxy).
- Module 01/06 (domain pointed at the VPS).

## Concepts to learn
- What a reverse proxy does: TLS termination, routing by hostname/path,
  hiding internal service topology.
- TLS/HTTPS basics: certificates, Let's Encrypt, ACME challenge, auto-renewal.
- Caddy (automatic HTTPS, simple config) vs Nginx (more control, more config)
  — pick one; Caddy is the gentler starting point for a beginner.
- Why app containers should bind to an internal Docker network only, not
  publish ports to the host directly, once a proxy is in front of them.
- HTTP → HTTPS redirects, HSTS basics.

## Hands-on: apply to PairUp
1. Add a `caddy` (or `nginx` + `certbot`) service to your compose stack.
2. Write a Caddyfile (or nginx conf) that routes `pairup.yourdomain.com`
   to the backend service over the internal Docker network.
3. Remove the backend's direct port publish from `docker-compose.yml` —
   only the proxy exposes 80/443 to the host now.
4. Confirm automatic HTTPS works: visiting `https://pairup.yourdomain.com`
   shows a valid certificate with no browser warnings.
5. Confirm plain HTTP requests redirect to HTTPS.
6. Update the module 05 CD workflow's smoke test to hit the HTTPS URL.

## Definition of done
- [ ] The app is reachable at `https://` on your real domain with a valid,
      auto-renewing certificate.
- [ ] The backend container has no host port published — all traffic goes
      through the proxy.
- [ ] HTTP requests redirect to HTTPS.

## Stretch goals
- Add a second dummy service (e.g. a `/status` static page) routed by path
  through the same proxy, to practice multi-service routing.
- Add basic rate limiting at the proxy layer.

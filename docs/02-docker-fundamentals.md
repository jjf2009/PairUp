# 02 — Docker Fundamentals

## Goal
Understand what containers actually are, and containerize a minimal
version of PairUp's backend from a Dockerfile you wrote yourself.

## Why it matters for job-readiness
Containers are the base unit of almost every DevOps workflow from here on
(CI, CD, k8s all assume you understand images/containers/layers). Knowing
*why* Docker works, not just the commands, is what separates someone who
can debug a broken build from someone who can only copy-paste a Dockerfile.

## Prerequisites
- Module 01 (you'll eventually run these containers on that VPS).
- Docker Desktop or Docker Engine installed locally.

## Concepts to learn
- What a container actually is: namespaces + cgroups, not a VM.
- Images vs containers vs layers; why layer caching makes builds fast.
- Dockerfile instructions: `FROM`, `WORKDIR`, `COPY`, `RUN`, `EXPOSE`, `CMD`
  vs `ENTRYPOINT`.
- `.dockerignore` and why it matters for build speed/security.
- Multi-stage builds (build stage vs slim runtime stage).
- Container networking basics: published ports (`-p`), bridge network.
- Volumes vs bind mounts (just the concept — used properly in module 08).
- Image size/security basics: why `node:20` vs `node:20-alpine` vs
  `node:20-slim` matters.

## Hands-on: apply to PairUp
1. Scaffold a minimal backend: a Node/Express (or your stack of choice)
   app with one route (e.g. `GET /health` returning `200 OK`) and a
   placeholder route for what will become the editor session API.
2. Write a Dockerfile for it from scratch (don't copy one) using a
   multi-stage build: an install/build stage, then a slim runtime stage
   that only copies what's needed to run.
3. Add a `.dockerignore` (node_modules, `.git`, `.env`, etc.).
4. Build it: `docker build -t pairup-backend:dev .` — read the build output,
   understand which lines get cached on a rebuild.
5. Run it: `docker run -p 3000:3000 pairup-backend:dev`, hit `/health` from
   your host.
6. Compare image size with `docker images` for a naive single-stage build
   vs your multi-stage one.
7. Push the image to a registry for the first time (GitHub Container
   Registry — you'll automate this in module 04, but do it manually once
   so you understand `docker login`/`docker push`).

## Definition of done
- [ ] You can explain layer caching well enough to order Dockerfile
      instructions for maximum cache reuse (deps before source copy).
- [ ] Your image uses a multi-stage build and is meaningfully smaller than
      a naive single-stage version.
- [ ] `docker run` starts the container and `/health` responds.
- [ ] You've manually pushed an image to GHCR at least once.

## Stretch goals
- Add a non-root `USER` in the Dockerfile and fix whatever permission
  errors that causes (this is real-world; skipping it is the #1 container
  security mistake).
- Try `docker scout` or `trivy image` against your image and read the
  vulnerability report (full treatment in module 14).

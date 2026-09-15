# 06 — Infrastructure as Code with Terraform

## Goal
Provision the VPS, its firewall, and its DNS records entirely from
Terraform code, reproducibly, instead of clicking through a dashboard.

## Why it matters for job-readiness
"I clicked buttons in a console" doesn't scale and isn't hireable. IaC —
specifically Terraform — is one of the most commonly listed requirements
in DevOps job postings. This module turns module 01's manual setup into
something you can destroy and rebuild in minutes.

## Prerequisites
- Module 01 (you know what you're about to automate, because you did it
  by hand first).
- A Terraform-supported provider account (Hetzner Cloud or DigitalOcean
  both have solid providers).

## Concepts to learn
- Providers, resources, data sources — the core Terraform vocabulary.
- State: what `terraform.tfstate` is, why it must not be committed to git,
  why remote state (S3-compatible bucket, Terraform Cloud, etc.) matters
  once more than one person/machine runs Terraform.
- Plan/apply/destroy workflow and why `terraform plan` before `apply` is
  non-negotiable.
- Variables (`variables.tf`) and outputs (`outputs.tf`).
- Idempotency: running `apply` twice with no changes should do nothing.
- Importing existing infra vs recreating from scratch (you'll likely
  recreate, since your module 01 VPS was manual).

## Hands-on: apply to PairUp
1. Install Terraform, initialize a `terraform/` directory in the repo.
2. Configure the provider (Hetzner/DigitalOcean) with an API token passed
   via environment variable — never hardcoded (ties into module 09).
3. Write resources for: the VPS itself, a firewall (mirroring the `ufw`
   rules from module 01), and the DNS A record for your domain.
4. Set remote state (even a simple S3-compatible bucket like Backblaze B2
   or a Terraform Cloud free workspace) so state isn't only on your laptop.
5. `terraform plan`, review every line, then `terraform apply`.
6. Destroy the module-01 manual VPS, confirm the Terraform-managed one
   works identically (SSH access, firewall rules, DNS).
7. Make one deliberate infra change (e.g. bump VPS size) purely by editing
   Terraform code and re-applying — no manual dashboard clicks.

## Definition of done
- [ ] The entire VPS + firewall + DNS setup exists as Terraform code in
      the repo, with `terraform.tfstate` excluded via `.gitignore` (state
      lives remotely, not in git).
- [ ] `terraform destroy` followed by `terraform apply` fully rebuilds a
      working VPS from nothing.
- [ ] You can explain what `terraform plan` would do before running it,
      for any change you make.

## Stretch goals
- Split into modules (`modules/vps`, `modules/dns`) even though you only
  have one environment — it's good practice for when you have staging/prod.
- Add a CI job that runs `terraform plan` on PRs that touch `terraform/`
  and posts the plan output as a PR comment.

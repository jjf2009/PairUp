# 01 — Linux & Networking Fundamentals

## Goal
Provision a bare Linux VPS, secure it, and understand enough networking
(ports, DNS, firewalls) to reason about anything you deploy on it for the
rest of this project.

## Why it matters for job-readiness
Every DevOps role assumes you're comfortable on a Linux box with no GUI.
Managed platforms hide this; interviewers will still ask you to explain
SSH keys, file permissions, systemd, and basic networking. This module is
the floor everything else stands on.

## Prerequisites
None. This is module 1.

## Concepts to learn
- SSH: key-based auth vs password auth, `~/.ssh/authorized_keys`, `ssh-agent`.
- Linux users/groups, `sudo`, principle of least privilege (don't work as root).
- Package management (`apt`), keeping a system patched.
- `systemd`: services, `systemctl start/enable/status`, reading logs with `journalctl`.
- Firewalls: `ufw` (or `nftables`/`iptables` conceptually), default-deny inbound.
- Networking basics: what a port is, TCP vs UDP, `/etc/hosts`, DNS A/AAAA
  records, what happens when you type a domain in a browser.
- Basic process/resource inspection: `top`/`htop`, `df -h`, `free -h`.

## Hands-on: apply to PairUp
1. Spin up a small VPS by hand through your provider's dashboard (Hetzner
   CX22 or DigitalOcean $6 droplet, Ubuntu LTS). You'll replace this
   manual step with Terraform in module 06 — doing it manually first is
   what makes Terraform click later.
2. SSH in as root once, then:
   - Create a non-root user with `sudo` access.
   - Copy your SSH public key to that user's `authorized_keys`.
   - Disable root SSH login and password auth in `sshd_config`; restart `sshd`.
3. Install `ufw`, allow only SSH (22) for now, enable it, verify you can
   still connect (don't lock yourself out — test in a second terminal
   before closing the first).
4. Buy or reuse a domain, and point an A record at the VPS's IP. Confirm
   with `dig`/`nslookup` that it resolves.
5. Write down (in a personal notes file, not committed) the VPS IP, domain,
   and SSH user — you'll need these in every later module.
6. Practice reading logs: `journalctl -u ssh -n 50` and `journalctl -xe`.

## Definition of done
- [ ] You can SSH into the VPS as a non-root user with a key (no password).
- [ ] Root login and password auth are disabled.
- [ ] `ufw status` shows default-deny with only SSH open.
- [ ] Your domain resolves to the VPS IP.
- [ ] You can explain, out loud, the difference between TCP and UDP and
      why that matters for a web app vs a video call.

## Stretch goals
- Set up unattended security updates (`unattended-upgrades`).
- Add a second SSH key from a different device and understand key rotation.
- Try `fail2ban` early (it's covered properly in module 14) to see failed
  SSH attempts get blocked automatically.

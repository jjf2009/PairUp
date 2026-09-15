# PairUp

Real-time collaborative interview / pair-programming platform. Synced code
editor, video, real sandboxed code execution, and an AI-generated feedback
report — built specifically for practicing mock interviews with a friend
instead of cobbling together Zoom and a pastebin. This is a system-design
interview question, actually shipped, and the origin story is authentic:
you built it to prep for your own interviews. Forces: CRDTs (Yjs), WebRTC,
a real job-queue-backed execution engine, and — since people will actually
use it live — real UI/UX effort.

## Why this project exists

Beyond shipping the product, this repo doubles as a hands-on DevOps
learning project — going from a bare VPS to a monitored, secured,
autoscaling production system, one skill at a time.

See [docs/00-roadmap.md](docs/00-roadmap.md) for the full module-by-module
learning path (Linux fundamentals → Docker → CI/CD → Terraform → k3s →
monitoring → sandboxed execution → security → production runbook).

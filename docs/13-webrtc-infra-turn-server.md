# 13 — WebRTC Infra (TURN/STUN)

## Goal
Make PairUp's video call feature actually work between two real users on
different networks, by self-hosting a TURN/STUN server.

## Why it matters for job-readiness
WebRTC infra is a networking niche most DevOps engineers never touch, but
it's exactly the kind of "why doesn't this work on my WiFi but works on
my phone hotspot" problem that makes you genuinely useful on a team
shipping real-time features. It's also a great way to cement your NAT/
firewall understanding from module 01.

## Prerequisites
- Module 01 (NAT/firewall/networking fundamentals).
- Module 14's mindset is relevant here too, but do this module first —
  you need working video before hardening it.

## Concepts to learn
- Why WebRTC needs STUN in the first place: NAT traversal, discovering
  your public IP/port from behind a router.
- Why STUN alone isn't enough for some networks (symmetric NAT, corporate
  firewalls) and TURN relays media as a fallback.
- ICE candidates and the negotiation flow at a conceptual level (you don't
  need to reimplement it — the browser WebRTC API and a signaling channel
  handle it — but you need to understand *why* a TURN server fixes "call
  connects for me but not my friend").
- `coturn` as the standard self-hosted STUN/TURN server.
- Why TURN needs a wide UDP port range open, and the bandwidth/cost
  implications of relaying media (TURN traffic isn't free — it flows
  through your server).
- TURN authentication (time-limited shared-secret credentials) so your
  relay isn't an open proxy for the whole internet.

## Hands-on: apply to PairUp
1. Deploy `coturn` on the VPS (as its own container/Deployment, alongside
   the rest of the k3s-managed stack).
2. Open the required ports in your module-01/06 firewall config: the TURN
   listening port plus a UDP relay port range — size the range deliberately,
   don't just open everything.
3. Configure time-limited TURN credentials (shared secret + expiring
   username, generated server-side, not static/hardcoded) so the backend
   API issues fresh credentials per call.
4. Wire the frontend's WebRTC `RTCPeerConnection` to use your STUN and
   TURN server URLs alongside (or instead of) a public STUN server.
5. Test across a real NAT boundary: two networks that aren't the same
   WiFi (e.g. your laptop + a phone on mobile data) and confirm the call
   connects. Force a TURN-only test by temporarily blocking direct/STUN
   paths to confirm the relay path actually works, not just the easy case.
6. Add TURN relay bandwidth to your module-11 monitoring — it's the one
   resource here that scales with usage in a way containers/CPU don't.

## Definition of done
- [ ] A video call connects reliably between two users on different, real
      networks (not just two tabs on the same machine).
- [ ] You've proven the TURN relay path works specifically, not just the
      STUN-only happy path.
- [ ] TURN credentials are short-lived and generated per session, not a
      static shared secret baked into the frontend.

## Stretch goals
- Add TURN-over-TLS (port 443) for networks that block arbitrary UDP/TCP
  ports entirely.
- Track relay bandwidth per call and think through what it would cost at
  real scale — this is the part that makes WebRTC infra expensive, and
  interviewers like candidates who've actually thought about it.

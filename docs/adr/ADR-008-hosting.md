# ADR-008: Hosting — Cloudflare Pages + R2 + Workers

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §4.4 (DR), §5.2 (CSP and headers), §5.5 (webhook worker)

## Context

Phase 1 is static SPA + one lightweight webhook endpoint. We need:
- Global CDN for the static app.
- Object storage for ~150 MB of Salamander samples (cost predictable, bandwidth-friendly).
- A small compute endpoint for the Stripe webhook.
- Strong security posture (SAQ-A compatible, hardware-MFA account hygiene).

## Decision

**Use Cloudflare Pages for the static app, Cloudflare R2 for samples, and Cloudflare Workers for the Stripe webhook.**

- Cloudflare account protected by hardware MFA (YubiKey × 2, primary + backup, §5.8).
- Scoped deploy tokens for CI; no long-lived admin credentials in GitHub Actions.
- R2: samples served via signed URLs; public bucket only for CDN-equivalent paths.
- Worker: `/stripe/webhook` endpoint only; Workers Secrets store the Stripe signing secret and Ed25519 license-signing private key.
- Cloudflare Web Analytics for privacy-preserving traffic metrics (no cookies).

## Consequences

- **Positive:**
  - Single vendor for v1 → simple billing, one dashboard, one SLA.
  - R2 egress is $0 — sample delivery at scale is economical.
  - Workers provide low-latency webhook processing near Stripe's regions.
  - Hardware MFA + scoped tokens address audit CRIT-A11 (account hygiene).
- **Negative / trade-offs:**
  - Single-vendor risk. Mitigated by `MASTER-PLAN.md §4.4` DR posture: AWS S3 secondary for static fallback (Phase 2+) and Object-Locked audit-log backup.
  - Workers have a small runtime surface (no Node APIs, limited CPU time) — webhook handler designed accordingly.
- **Neutral:**
  - Supabase (Phase 2+) sits outside Cloudflare; cross-boundary traffic is acceptable at Phase-2 scale.

## Alternatives Considered

1. **Vercel + Vercel Blob** — rejected: egress pricing less favourable; no R2 equivalent at matching cost.
2. **Netlify + S3** — rejected: multi-vendor from day one increases ops burden at Phase-1 scale.
3. **Self-hosted (Hetzner/Fly.io)** — rejected: operational overhead incompatible with solo founder; static hosting solved problems should stay solved.
4. **AWS S3 + CloudFront + Lambda@Edge** — rejected: ops complexity and cost for a single webhook.

## References

- `MASTER-PLAN.md §3` (ADR-008)
- `MASTER-PLAN.md §4.4` (DR & BC)
- `MASTER-PLAN.md §5.5` (Stripe webhook security)
- Cloudflare Pages, R2, Workers docs

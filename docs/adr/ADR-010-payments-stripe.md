# ADR-010: Payments — Stripe Checkout (hosted, SAQ-A scope)

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §5.5 (Stripe integration security), §5.9 (compliance roadmap)

## Context

Phase 1 charges $29 / TRY 999 / €27 lifetime (geo-priced). We need the lowest-effort path to:
- EU VAT compliance (TR+EU geo, ADR-018).
- PCI DSS compliance at minimum burden.
- Strong payment authentication (3DS forced in EU).
- Reliable webhook-driven license issuance with idempotency and replay protection.

## Decision

**Use Stripe Checkout (hosted) as the only payment surface. Stripe Elements is explicitly not used.**

- Hosted Checkout keeps us at **PCI SAQ-A** — the narrowest scope.
- **Stripe Tax** enabled from Day 1 for EU VAT.
- **Webhook verification**: `stripe.webhooks.constructEvent()` with timing-safe HMAC, raw body, 5-minute replay window.
- **Idempotency**: handler upserts on `stripe_event_id` (KV in Phase 1; Postgres unique constraint in Phase 2+).
- **Server-side price lookup** (`STRIPE_PRICE_LIFETIME_TRY/_EUR/_USD`). Client never provides the price.
- **3DS forced in EU** via Stripe Radar rules.
- **License key issuance**: Ed25519-signed JWT-shaped token payload `{email_hash, sku, issued_at, version}`; client-side verification.
- **Refund SOP** documented; refunds trigger license revocation via revocation list in KV.

## Consequences

- **Positive:**
  - SAQ-A is the cheapest PCI posture — self-attestation only, no audit.
  - Stripe Tax eliminates an otherwise-expensive compliance workstream.
  - Ed25519-signed license tokens let the app verify entitlement offline (no auth service in Phase 1).
  - Checkout handles localisation, card-brand updates, regulator-driven UX changes automatically.
- **Negative / trade-offs:**
  - Checkout UX is branded with Stripe's template — less visual control than Elements. Acceptable trade-off; brand lives in the app proper.
  - Stripe Tax fee on each transaction (small percentage); factored into pricing.
- **Neutral:**
  - Dispute and chargeback workflow handled via Stripe dashboard + Worker updates to the revocation list.

## Alternatives Considered

1. **Stripe Elements** — rejected: pushes us to PCI SAQ-D; dramatically expands security posture. Not justified at Phase 1 scale.
2. **Paddle** — rejected: merchant-of-record model simplifies tax but cedes control of the checkout UX and support path; Stripe + Stripe Tax achieves similar benefit with better long-term flexibility.
3. **LemonSqueezy** — rejected: smaller ecosystem; less mature webhook/security tooling.
4. **Custom payment integration + payment gateway** — rejected: SAQ-D scope, no offsetting benefit.

## References

- `MASTER-PLAN.md §3` (ADR-010)
- `MASTER-PLAN.md §5.5` (Stripe integration security)
- `MASTER-PLAN.md §5.9` (compliance roadmap, including PCI SAQ-A)
- Stripe Checkout, Stripe Tax, Stripe Webhooks docs

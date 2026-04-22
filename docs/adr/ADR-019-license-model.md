# ADR-019: License model v1.0 — $29 lifetime perpetual via emailed license key

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §2.4 (Phase 1 scope), §5.5 (license issuance), §8.1 (pricing test)

## Context

Phase 1 must validate willingness-to-pay on a narrow corpus. Introducing user accounts just to deliver access multiplies security surface (authN/Z, session management, password reset, MFA) without improving the validation question ("will pianists pay $29 for this?"). A purely license-key-based access model eliminates the entire auth attack surface and simplifies refunds/disputes.

## Decision

**v1.0 license model: $29 one-time lifetime perpetual. Access granted by an Ed25519-signed JWT-shaped license token, emailed to the buyer by the Stripe webhook handler. Client-side signature verification grants access. No user accounts exist in v1.0.**

- Payload: `{email_hash, sku, issued_at, version}` — no PII beyond email hash.
- Storage: browser localStorage; also recoverable from the buyer's email at any time.
- Geo-priced at Stripe Checkout: $29 / TRY 999 / €27 (§8.1).
- Refund SOP: full refund within 14 days (GDPR distance-selling baseline); refund triggers license revocation in KV (§5.5).
- Revocation list in Cloudflare KV; client checks on app boot when online.

## Consequences

- **Positive:**
  - **Zero auth attack surface** — no password, no session, no reset flow, no MFA.
  - Refund and dispute workflows simpler; webhook → KV → revocation is linear.
  - User UX friction minimal: one email, one link, perpetual access on any supported browser.
  - Eliminates 4/6 security CRITICAL findings in Phase 1 (§2.4).
- **Negative / trade-offs:**
  - License token is user-copyable; sharing is technically possible. Mitigated: small price point reduces motivation; Stripe Radar + Ed25519 binding to `email_hash` at purchase; revocation list blocks egregiously-shared keys.
  - Multi-device requires re-using the license link per device — no "sync account" story. Accepted per ADR-013.
- **Neutral:**
  - Phase 2 introduces Supabase Auth for subscription users; lifetime license holders retain perpetual access via their key.

## Alternatives Considered

1. **Full account-based auth** — rejected at Phase 1: scope bloat; security surface unjustified before willingness-to-pay validation.
2. **Subscription from day 1** — rejected: the $29 lifetime is a validation-friendly price point; subscription is a Phase 3 play after G2 proves demand.
3. **Free trial + paywall** — rejected: dilutes the willingness-to-pay signal that G2 must measure.
4. **Magic link login (no password, no token)** — rejected: still requires backend session infra; no simpler than a signed token.

## References

- `MASTER-PLAN.md §3` (ADR-019)
- `MASTER-PLAN.md §2.4` (Phase 1 scope rationale)
- `MASTER-PLAN.md §5.5` (Stripe integration security)
- `MASTER-PLAN.md §8.1` (Sprint 0 pricing test)

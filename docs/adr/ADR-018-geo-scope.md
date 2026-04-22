# ADR-018: Geo scope v1.0 — TR + EU only

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1, 2
- **Master Plan reference:** §3, §5.9 (compliance roadmap), §10.2 (compliance posture)

## Context

Each jurisdiction introduces a distinct compliance regime (CCPA/CPRA for California, LGPD for Brazil, UK DPA post-Brexit divergence, COPPA for US under-13, various state privacy acts). Serving all of them from Phase 1 multiplies legal counsel cost, privacy-notice variants, age-policy branching, and response-to-DSR complexity. A solo founder cannot carry that surface while shipping a product.

## Decision

**Phase 1 and Phase 2 serve only Turkey (KVKK) and EU/EEA (GDPR) users. Non-TR/EU traffic is discouraged at the payment and signup steps. US, UK, and BR launches are deferred.**

- Stripe Checkout restricted to supported countries via Stripe's `allowed_countries` parameter.
- Landing-page copy reflects TR + EU availability.
- Privacy policy + ToS in Turkish and English; German added Phase 2.
- KVKK cross-border consent explicit in Stripe Checkout for TR users.
- Standard Contractual Clauses (SCCs) signed with Stripe, Cloudflare, Sentry, Honeycomb before launch (§5.9).

## Consequences

- **Positive:**
  - Compliance surface narrows to KVKK + GDPR + ePrivacy + EAA 2025 + PCI SAQ-A — a known quantity.
  - Legal budget is contained (§9.2: €1.5k Phase 1 counsel).
  - ADR-017 (16+ hard gate) composes cleanly: no COPPA contingency needed because US isn't served.
  - Product can iterate quickly without cross-jurisdictional privacy-notice churn.
- **Negative / trade-offs:**
  - Addressable market is narrower in Phase 1. Acceptable because Phase 0 CAC validation (§2.3) targets TR + DE audiences explicitly.
  - Users in deferred geos who find the site will experience friction; copy sets expectation clearly.
- **Neutral:**
  - Phase 3 (conditional on G2/G3) re-opens the geo question. Likely order: UK (post-Brexit DPA is close to GDPR) → US (requires CCPA/CPRA + COPPA consideration) → BR.

## Alternatives Considered

1. **Global launch** — rejected: compliance surface unbounded at Phase 1; legal cost escalates; audit CRIT-S5 risk.
2. **TR only (domestic-first)** — rejected: TAM too narrow for the validation thesis; product is EN-capable from day 1.
3. **EU + UK** — rejected: UK DPA + ICO interaction adds a second regulator for marginal TAM gain at Phase 1.
4. **EU + US** — rejected: COPPA/CCPA/CPRA workload incompatible with solo Phase 1.

## References

- `MASTER-PLAN.md §3` (ADR-018)
- `MASTER-PLAN.md §5.9` (compliance roadmap)
- `MASTER-PLAN.md §10.2` (compliance posture by phase)

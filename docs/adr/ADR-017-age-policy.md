# ADR-017: Children's data policy — 16+ hard age gate, no COPPA pathway in v1.0

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1, 2
- **Master Plan reference:** §3, §5.4 (children's data policy), closes audit CRIT-S4

## Context

The v1.0 plan implicitly targeted the "Hoffman demographic" (broad beginner including children) without addressing COPPA, GDPR Art. 8, or KVKK child-consent requirements. Audit CRIT-S4 flagged this: any service to under-16 in the EU (Art. 8 highest-bar interpretation) or under-13 in the US (COPPA) without VPC machinery is an unacceptable legal risk for a solo founder.

## Decision

**Service is 16+ only in v1.0 and v2.0. No COPPA pathway. Under-16 users are not served.**

Implementation:
- **Phase 1**: footer disclaimer + age-confirmation checkbox in the Stripe Checkout terms.
- **Phase 2** (with auth): DOB at signup; reject if under 16 (GDPR Art. 8 highest-bar applied uniformly to simplify KVKK by avoiding the explicit-consent-for-child path entirely).
- Rate-limit re-attempts per IP/email.
- Privacy policy includes: "We do not knowingly collect data from children under 16. If you become aware that a child has provided us personal data, contact us for deletion."
- No COPPA VPC infrastructure. US users are geo-blocked (ADR-018) and therefore out of COPPA scope anyway.
- Re-opens for review at v3.0 only, with proper VPC infrastructure and a business case to justify it.

## Consequences

- **Positive:**
  - Closes audit CRIT-S4 with the simplest legal posture for a solo founder.
  - Eliminates parental-consent UX, VPC vendor integration, and child-specific data handling — all expensive to build and audit.
  - Clean privacy story for the privacy policy and the KVKK cross-border consent flow.
  - Marketing copy already aligns ("serious amateurs / pre-professionals").
- **Negative / trade-offs:**
  - Explicitly excludes a user segment (children, families). Lost potential revenue. Accepted trade-off at Phase 1/2 scale.
  - Enforcement is soft (age checkbox is self-reported). Mitigated by combining with payment-method (must be an adult cardholder) and, post Phase 2, DOB collection.
- **Neutral:**
  - v3.0 may re-open this with investor-backed VPC build if the B2B/EDU pipeline justifies.

## Alternatives Considered

1. **13+ with GDPR member-state threshold opt-in** — rejected: most member states (DE, FR, IT, NL) set threshold at 16; uniform 16 simplifies.
2. **COPPA-compliant pathway for under-13** — rejected: VPC vendor cost + ops overhead incompatible with solo-founder Phase 1.
3. **Under-16 allowed with parental email** — rejected: still requires VPC rigor under KVKK and several EU member states; half-measure with full liability.

## References

- `MASTER-PLAN.md §3` (ADR-017)
- `MASTER-PLAN.md §5.4` (children's data policy)
- GDPR Art. 8; KVKK child-consent; COPPA (historical — US deferred per ADR-018)

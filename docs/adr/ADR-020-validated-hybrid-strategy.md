# ADR-020: Strategy — Validated Hybrid Approach (Option C → B → A)

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 0, 1, 2, 3
- **Master Plan reference:** §2.1 (strategic path), §2.2 (phase roadmap), §11 (gates)

## Context

The audit established three project-killer risks (PK-1 curriculum cost, PK-2 CAC assumptions, PK-3 solo-founder timeline). Pure Option A (full plan) requires €175–300k and high-covariance simultaneous assumption success. Pure Option B (Bach MVP only) validates cheaply but cannot by itself justify the broader premium-platform thesis. A hybrid that spends ~2–3 weeks measuring the dominant assumptions before committing offers asymmetric information value at negligible cost (€2–3k + 100 h Phase 0) relative to the downstream commitment.

## Decision

**Adopt the Validated Hybrid Approach: Phase 0 validation → Phase 1 single-domain MVP (Bach) → conditional Phase 2 expansion → conditional Phase 3 premium. Three decision gates (G1, G2, G3) protect against proceeding on unvalidated assumptions.**

Timeline (§2.2):

| Phase | Weeks | Hours | Cash |
|---|---|---|---|
| Phase 0 — Validation | 1-3 | ~100 | €2-3k |
| Phase 1 — Foundation MVP | 4-15 | ~350 | €4-6k |
| Phase 2 — Expansion (conditional) | 16-24 | ~250 | €4-8k |
| Phase 3 — Premium (conditional) | 25-52 | ~600 | €8-15k |
| **Y1 total** | **52** | **~1,300** | **€18-32k** |

Gates (§11):
- **G1 (Week 3):** CPA ≤ €15 to email, intent ≥ 5%, 10/15 interview validation, prototype p95 audio latency ≤ 50 ms. Fail → reposition once or sunset.
- **G2 (Week 20):** ≥ 100 paying customers at $29, refund < 10%, NPS ≥ 30, avg session ≥ 12 min, ≥ 1 organic teacher mention. Fail → root-cause, repair-and-retry once, or sunset.
- **G3 (Week 24):** ≥ 250 cumulative paying, ≥ 3% expanded-catalog conversion, ≥ 30% week-4 retention, bandwidth + cash for Phase 3. Fail → harvest as profitable side-project, defer Phase 3.

## Consequences

- **Positive:**
  - Each phase's cash and time are contingent on prior-phase validation, not a bet made at Week 0.
  - Phase 0 paid-acquisition test directly measures PK-2 (CAC).
  - Phase 1 Bach-only scope keeps PK-1 (curriculum cost) dormant until G2/G3 data would justify it.
  - Phase 3 conditionality respects PK-3 (solo timeline) — committing only if the economics support it.
  - Hard exit options at every gate — the project has four legitimate endings (ship Phase 3, harvest Phase 2, pivot after G1, sunset).
- **Negative / trade-offs:**
  - Slower to full-product vision than Option A would have been (if Option A's premises all held, which they may not).
  - Requires the founder to credibly commit to the exit conditions — psychological risk of sunk-cost bias at each gate.
- **Neutral:**
  - Phase 3 remains underspecified in the Master Plan; a separate quality-gate cycle re-plans Phase 3 at Week 22 with G2 data in hand (§2.6).

## Alternatives Considered

1. **Option A (full plan, Week 0 commitment)** — rejected by audit; PK-1/PK-2/PK-3 covariance unacceptable.
2. **Option B (Bach MVP only, no validation sprint)** — rejected: skips the cheap measurement; leaves PK-2 untested.
3. **Pure customer-discovery-only Phase 0 (no paid test)** — rejected: qualitative interviews don't test CAC; paid test is the cheapest real signal.
4. **12-week sprint to v1.0** — rejected as "delusional" in the audit (§1.2); solo founder with 25 h/week cannot deliver the v1.0 scope in 12 weeks.

## References

- `MASTER-PLAN.md §2.1` (why hybrid)
- `MASTER-PLAN.md §2.2` (phase roadmap)
- `MASTER-PLAN.md §11` (decision gates)
- `docs/_archive/AUDIT-REPORT-V1.md` (project-killer risks PK-1/2/3)

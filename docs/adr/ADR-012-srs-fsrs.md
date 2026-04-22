# ADR-012: SRS — ts-fsrs (FSRS-4.5)

- **Status:** Accepted (Phase 2+)
- **Date:** 2026-04-17
- **Phase:** 2, 3
- **Master Plan reference:** §3, §1.1 (value prop), §7.2 (Phase 2 corpus)

## Context

One of Phase 1's product-thesis pillars is **spaced repetition for repertoire maintenance** — borrowed from language-learning. Pianists routinely "drop" pieces they used to play. A good SRS schedules review days so that pieces stay playable with minimal time investment. For Phase 1 with 15 pieces and a license-only model, a simple review-due date is adequate. Phase 2's 30+ pieces and account system materially benefit from a modern scheduling algorithm.

## Decision

**Use `ts-fsrs` (TypeScript implementation of FSRS-4.5) as the SRS engine starting in Phase 2.**

- Phase 1: simple heuristic review scheduling (not SRS; placeholder in the UI).
- Phase 2: `ts-fsrs` driving actual review cadence, stored per-piece-per-user.
- User ratings: "Again / Hard / Good / Easy" after each practice session; ratings feed the FSRS update.
- Parameters default to published FSRS-4.5 weights; personalised weights (per-user `optimizer`) are a Phase 3 option, DPIA-gated.

## Consequences

- **Positive:**
  - MIT licensed; TypeScript-native; minimal runtime footprint.
  - FSRS-4.5 outperforms SM-2 across published benchmarks; a better product for serious amateurs maintaining repertoire.
  - Default weights work well without personalisation — Phase 2 can ship without DPIA overhead.
- **Negative / trade-offs:**
  - FSRS requires per-item state (stability, difficulty, last review). Modest storage cost; negligible vs. practice-session payloads.
  - User UX — rating after every session — adds friction. Mitigation: minimal, one-tap rating; skippable.
- **Neutral:**
  - Personalised parameters (Phase 3) shift from aggregate to per-user behaviour; triggers DPIA and explicit consent UX.

## Alternatives Considered

1. **SM-2 (Anki-classic)** — rejected: FSRS is demonstrably better; implementation cost is the same.
2. **Custom scheduling** — rejected: no reason to re-invent a researched domain.
3. **No SRS, just "practice more"** — rejected: removes a core product-thesis pillar; audit-noted differentiator.

## References

- `MASTER-PLAN.md §3` (ADR-012)
- `MASTER-PLAN.md §1.1` (product-thesis pillar #5)
- FSRS-4.5 paper; `ts-fsrs` GitHub

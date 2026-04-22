# Gate G1 — Decision Record (TEMPLATE, filled Week 3)

> **Status:** TEMPLATE — to be completed by founder at end of Phase-0 Week 3, target **<YYYY-MM-DD>** (Friday, Week 3).
> **Scope:** Formal decision record for Gate G1 per `MASTER-PLAN.md §11`.
> **Authority:** The founder, operating under Senior Staff Engineer authority.
> **Audience:** Self, archive, any future partner / investor / successor.

---

## 0. Pre-Commitment (signed **before** reviewing data)

> *Before I review the evidence I commit that I will abandon the current positioning if any of the five G1 criteria fail, unless I can state — on paper — a specific, testable reason why the failure is instrument-error and not signal. I acknowledge that sunk-cost bias will bias me toward "one more retry" and that the Master Plan permits at most one retry cycle.*

**Signed:** ________________________
**Date:** <YYYY-MM-DD>

*(Sign this before opening any Phase-0 results. The signature is the point.)*

---

## 1. Criterion-by-Criterion Evidence

### C1 — Paid acquisition CPA ≤ €15 to email signup

| Field | Value |
|---|---|
| Target | ≤ €15 |
| Observed CPA | **€<X>** |
| Spend total | €<Y> (Meta €<a>, Google €<b>) |
| Email signups | <N> |
| Evidence source | `../research/PHASE0-RESULTS.md §1` + Plausible CSV export |
| Confidence (H/M/L) | <H/M/L> — rationale: <why> |
| **Pass/Fail** | <PASS / FAIL> |

**Notes:**
- <Unexpected audience signals, channel split, creative that outperformed, etc.>

### C2 — Intent-to-pay rate ≥ 5%

| Field | Value |
|---|---|
| Target | ≥ 5% of landing-page visitors clicking "Yes, I'd pay $29" |
| Observed intent rate (stated) | **<X>%** |
| Revealed-preference cross-check | <M>% opened the waitlist follow-up email sent Week 3 |
| Discounted intent (apply 30–50% to stated) | **<X'>%** |
| Evidence source | `../research/PHASE0-RESULTS.md §2` + Plausible event log |
| Confidence (H/M/L) | <H/M/L> |
| **Pass/Fail (discounted)** | <PASS / SOFT-PASS / FAIL> |

**Notes:**
- <Differences between TR and EU audiences, intent consistency across creatives.>

### C3 — Interviews validate pain + willingness-to-pay (≥ 10/15)

| Field | Value |
|---|---|
| Target | ≥ 10 / 15 interviews satisfy pain Y ∩ WTP Y ∩ JTBD Y |
| Completed interviews | <N>/15 |
| Segment split | Teachers <n>, self-learners <n>, advanced <n> |
| Pass count (all 3 tags Y) | <K>/<N> |
| Evidence source | `../research/INTERVIEWS-PHASE0/SYNTHESIS.md` |
| Confidence (H/M/L) | <H/M/L> — sample size, segment coverage |
| **Pass/Fail** | <PASS / FAIL> |

**Key verbatim quotes (anonymised):**
- > "<quote>"
- > "<quote>"
- > "<quote>"

**Common JTBD statement (one sentence):**
- <draft>

---

### C4 — Pedagogue quotes support license-then-extend OR Bach-niche viability

| Field | Value |
|---|---|
| Target | ≥ 1 of the 2 paths defensible from 3 pedagogue quotes |
| Completed consultations | <N>/3 |
| Licensing 1-grade-level (€, months) range | €<low>-<high>, <n>-<m> months |
| Authoring 1-grade-level (€, months) range | €<low>-<high>, <n>-<m> months |
| Pedagogue verdict on Bach-niche viability | <positive / mixed / negative>; quoted sentiment |
| Evidence source | `../research/PHASE0-RESULTS.md §4` + pedagogue notes |
| Confidence (H/M/L) | <H/M/L> |
| **Pass/Fail** | <PASS / FAIL> |

**Notes:**
- PK-1 audit estimate (€100–150k for 25 levels) — **validated / refuted / refined** to: **<…>**.
- Recommended Phase-3 path: **<license-then-extend / author-from-scratch / Bach-niche-stay>**.

---

### C5 — Audio latency p95 ≤ 50 ms + no platform-killer

| Field | Value |
|---|---|
| Target | p95 ≤ 50 ms on test hardware, no unrecoverable blocker |
| macOS + wired MIDI p95 | <X> ms |
| Windows + wired MIDI p95 | <X> ms |
| Linux + wired MIDI p95 | <X> ms (if tested) |
| iOS Safari audio playback | <OK / degraded / blocked> — note iOS has **no WebMIDI** (documented limitation, not a blocker) |
| Android Chrome + wired MIDI p95 | <X> ms |
| Notation render p95 (BWV 772) | <X> ms |
| Sample load time (Lite) | <X> s on 4G |
| Platform blockers observed | <list or NONE> |
| Evidence source | `prototype/TEST-RESULTS.md` |
| Confidence (H/M/L) | <H/M/L> |
| **Pass/Fail** | <PASS / PARTIAL / FAIL> |

---

## 2. Outlier & Surprise Log

Top 3 things I did **not** expect. These are often the information value of the sprint.

1. <Surprise 1 — with evidence>
2. <Surprise 2>
3. <Surprise 3>

---

## 3. Revealed-Preference Cross-Check (P0-R5)

- **Apply 30–50% discount** to stated intent before calling C2 a pass.
- Does the revealed-preference proxy (waitlist-email open rate) corroborate the stated intent?
- **C2 holds under discount: <YES / NO / SOFT>.**

---

## 4. Composite Judgement

| Criterion | Result | Confidence |
|---|---|---|
| C1 CPA | <P/F> | <H/M/L> |
| C2 Intent | <P/SP/F> | <H/M/L> |
| C3 Interviews | <P/F> | <H/M/L> |
| C4 Pedagogue | <P/F> | <H/M/L> |
| C5 Technical | <P/PARTIAL/F> | <H/M/L> |

**ALL-GO requires:** C1 + C2 (discounted) + C3 + C4 + C5 all PASS with confidence ≥ M.

---

## 5. Decision

Exactly one of the following:

### ☐ GO — Proceed to Phase 1

**Rationale:** <1–2 sentences, referencing the evidence above.>
**Named next action:** <concrete, dated>: begin Phase-1 Week 4 on <YYYY-MM-DD> with the stack per ADR-001..020.
**Owner:** Founder.

### ☐ PIVOT — One retry cycle

**Failing criteria:** <which ones>.
**Hypothesis for retry:** <specific, testable>. Options: reposition landing copy; shift audience; adjust price anchor; narrow to B2B teacher tool.
**Retry budget:** <hours + €>, cap to 2 weeks.
**Retry stop-go criteria:** <what would make me proceed / sunset at end of retry>.
**Retry start date:** <YYYY-MM-DD>.

### ☐ SUNSET — Close the project cleanly

**Rationale:** <failed criteria + low-confidence repair signal OR retry exhausted>.
**Close-down plan:**
- [ ] Pause ads; refund any stranded intent signups with an explanatory email.
- [ ] Post a public post-mortem (anonymised) on <YYYY-MM-DD>.
- [ ] Archive the repository; preserve MASTER-PLAN and this G1-DECISION for learning.
- [ ] Cancel Plausible, MailerLite, Whereby subscriptions.
- [ ] Close Stripe account (if opened).
- [ ] Withdraw outstanding pedagogue payment obligations.
- [ ] Reflect: what I'd do differently (brief essay).

---

## 6. Budget Reconciliation

| Line item | Budgeted | Actual | Delta |
|---|---|---|---|
| Meta Ads | €500 | €<x> | |
| Google Ads | €300 | €<x> | |
| Domain + email | ~€40 | €<x> | |
| Cloudflare | ~€10 | €<x> | |
| Plausible | €9–19 | €<x> | |
| MailerLite | €0 | €<x> | |
| Whereby | €0 | €<x> | |
| Interview incentives | ~€450 | €<x> | |
| Pedagogue consultations | ~€360 | €<x> | |
| YubiKey (if purchased) | ~€100 | €<x> | |
| Contingency | ~€100 | €<x> | |
| **Total** | **~€1,489–€1,879** | €<x> | |

---

## 7. Time Reconciliation

| Workstream | Budgeted h | Actual h |
|---|---|---|
| WS1 Paid acquisition | 28 | <x> |
| WS2 Customer discovery | 24 | <x> |
| WS3 Pedagogue | 12 | <x> |
| WS4 Prototype | 32 | <x> |
| WS5 Synthesis & decision | 4 | <x> |
| **Total** | **100** | <x> |

---

## 8. Retrospective (5 bullets max)

- **What worked well:** <…>
- **What nearly went wrong:** <…>
- **Biggest surprise:** <…>
- **If I did Phase 0 again, I would:** <…>
- **Open questions carrying into Phase 1:** <…>

---

## 9. Signatures & Distribution

- **Decision signed by:** ________________________
- **Date:** <YYYY-MM-DD>
- **Archive location:** `docs/decisions/G1-DECISION.md` (committed to main)
- **Public post (if applicable):** <URL of blog post / newsletter>

---

## References

- `MASTER-PLAN.md §2.3`, §11
- [`PHASE0-SETUP.md`](PHASE0-SETUP.md)
- [`../research/PHASE0-RESULTS.md`](../research/PHASE0-RESULTS.md) — the underlying evidence
- [`../research/INTERVIEWS-PHASE0/SYNTHESIS.md`](../research/INTERVIEWS-PHASE0/SYNTHESIS.md)
- `prototype/TEST-RESULTS.md`

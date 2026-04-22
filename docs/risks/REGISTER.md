# Risk Register — Piano-Practice

> **Scope:** Live risk register. Seeded from `MASTER-PLAN.md §10.1`; augmented with Phase-0 risks and operational risks.
> **Review cadence:** per Gate (G1, G2, G3); monthly during active phases; on every SEV-1.
> **Owner:** Founder (Phase 1). Responsibilities per row may delegate to legal counsel.

---

## 1. Severity Model

- **Likelihood:** Very Low / Low / Medium / High / Very High.
- **Impact:** Cosmetic / Moderate / Major / Project-killer.
- **Risk score:** likelihood × impact (qualitative matrix).

---

## 2. Register (Master Plan §10.1 core rows)

| ID | Risk | L | I | Mitigation | Owner | Review |
|---|---|---|---|---|---|---|
| R-01 | CAC exceeds viability | M | Project-killer | Phase-0 paid test; G1 gate | Founder | Weekly Phase 0, at G1 |
| R-02 | Curriculum cost balloons | L (deferred) | Major | Phase 1 PD-only; Phase 3 decision gated by data | Founder | At Phase 3 gate |
| R-03 | Solo burnout / timeline slip | M-H | Major | 25 h/wk cap; monthly retro; G2 abandon clause | Founder | Monthly |
| R-04 | Stripe webhook compromise → free licenses | L | Major | HMAC verify + idempotency + revocation; rotation drill | Founder | Quarterly tabletop |
| R-05 | KVKK cross-border enforcement | L-M | Major | Explicit consent + SCCs + counsel review | Counsel | Annually |
| R-06 | Audio latency on iOS Safari unfixable | L | Major | Phase-0 prototype validates; fallback ScriptProcessorNode + UX warning | Founder | At G1 |
| R-07 | Bach corpus differentiation insufficient | M | Major | Depth over quantity (fingering reasoning, hand separation) | Founder | At G2 |
| R-08 | OSMD performance with large scores | L | Moderate | Phase-1 ≤ 200-bar pieces; pagination Phase 2+ | Founder | At Phase 2 plan |
| R-09 | Salamander CC-BY 3.0 interpretation | L | Major | Counsel review pre-launch | Counsel | At Phase 1 launch |
| R-10 | Cloudflare account compromise | VL | Catastrophic | Hardware MFA + scoped tokens + monthly audit | Founder | Monthly |

---

## 3. Additional Rows (operational + project-specific)

| ID | Risk | L | I | Mitigation | Owner | Review |
|---|---|---|---|---|---|---|
| R-11 | Jurisdiction extends PD term retroactively | VL | Major | Annual counsel review; `REPERTOIRE-PROVENANCE.md` audit | Counsel | Annually |
| R-12 | Stripe policy change blocks our SKU | L | Major | Maintain Paddle / LemonSqueezy contingency brief | Founder | Semi-annually |
| R-13 | Cloudflare Pages policy change blocks static hosting | VL | Major | DR plan §1 (S3 secondary Phase 2+) | Founder | Annually |
| R-14 | Sentry / Honeycomb free-tier limit hit | L | Moderate | Sampling rate throttle; upgrade path documented | Founder | Monthly |
| R-15 | Dependency supply-chain attack (npm) | L | Major | Renovate + `--ignore-scripts` + SBOM + gitleaks | Founder | Ongoing |
| R-16 | Domain hijack | VL | Catastrophic | Registrar lock + 2FA + monitoring | Founder | Quarterly |
| R-17 | Email deliverability (licenses in spam) | M | Major | Reputable transactional provider; SPF/DKIM/DMARC; in-app recovery flow | Founder | At launch |
| R-18 | Regulatory change (EU AI Act broadens) | M | Major | Phase-1 has no runtime AI → insulated; Phase 3 model card planning | Counsel | Annually |
| R-19 | Negative press about browser-based music tooling | L | Moderate | Transparent changelog; prompt customer communication | Founder | Ad hoc |
| R-20 | Competitor launches same niche | M | Moderate | Depth-over-breadth + pedagogical credibility + community | Founder | Quarterly |
| R-21 | iOS browser autoplay policy change | L | Major | Keep explicit gesture path; monitor Safari release notes | Founder | Ongoing |
| R-22 | GitHub outage during release | VL | Moderate | Emergency path: deploy from local clone | Founder | Annually |
| R-23 | Third-party font hosting deprecation | VL | Cosmetic | We self-host; no risk Phase 1 | — | — (self-hosted) |

---

## 4. Phase-0 Specific Risks (from `/plan` output)

| ID | Risk | L | I | Mitigation | Owner | Review |
|---|---|---|---|---|---|---|
| P0-R1 | Meta/Google ad account approval delay | H | H | Create Day 1; 2 creative variants ready | Founder | Daily |
| P0-R2 | Pedagogue outreach reply rate < 10 % | M | M | 15 outreach Day 1; paid consultation offer upfront | Founder | Weekly |
| P0-R3 | iOS Safari Web MIDI unsupported | H | M | iOS scoped to playback-only; documented in G1 record | Founder | At G1 |
| P0-R4 | Interview ethics / PII handling gap | M | H | Consent form; gitignored RAW/ folder; compensation | Founder | Before each interview |
| P0-R5 | Stated-intent inflates over revealed preference | H | M | Week-3 waitlist-email cross-check; apply 30–50 % discount at G1 | Founder | At G1 |
| P0-R6 | Ad budget exhausts before statistical signal | M | M | €110/day cap; Day-2 CTR review | Founder | Daily |
| P0-R7 | Landing copy targets under-16 accidentally | L | H | Copy checklist before publish | Founder | Pre-publish |
| P0-R8 | Prototype scope creep | M | M | 32 h hard cap; README states DISPOSABLE | Founder | Daily |
| P0-R9 | CF preview URL indexed by mistake | L | M | robots.txt check before ads launch | Founder | Pre-ad |
| P0-R10 | Founder incapacity during sprint | L | H | Executor envelope with pause-only credentials | Founder | Week 0 |

---

## 5. Risk Acceptance

A risk may be **accepted** (not mitigated) only with:

- Explicit founder decision logged here with date.
- Trigger conditions that would reopen mitigation (e.g., "if R-14 fires, upgrade to Sentry Team").
- Review date.

Current accepted risks:

- **R-23** accepted by design (self-hosted fonts).
- **R-13** accepted for Phase 1; mitigated Phase 2.
- **R-02** accepted for Phase 1 (curriculum cost deferred to Phase 3).

---

## 6. Changelog

- **2026-04-17:** Register initialised from Master Plan §10.1 and Phase-0 plan.

All updates append a dated entry here.

---

## 7. References

- `MASTER-PLAN.md §10.1`, §10.2
- [`../operations/IR-PLAN.md`](../operations/IR-PLAN.md)
- [`../operations/DR.md`](../operations/DR.md)
- [`../security/THREAT-MODEL.md`](../security/THREAT-MODEL.md)

# Piano-Practice — Project Progress & Session Log

> **Purpose:** Living status of what's been done, what's pending, and how to resume in a future session (human or AI).
> **Audience:** Founder + any Claude Code session that opens this repo.
> **Update policy:** Edit in the same commit that lands the progress delta. Never let this file drift.

---

## 1. Current Status (as of 2026-04-22)

| Field | Value |
|---|---|
| Phase | **Pre-Phase-0** (about to start Validation Sprint Week 1 Day 1) |
| Gate | G1 target: Week 3 end |
| Active branch | `claude/sharp-spence-f80ab3` (worktree) |
| Latest commits | `3224091` (Phase-0 prep), `49ba49a` (Tier-1 foundation) |
| Divergence from `main` | Ahead by **2 commits** (this branch); rebased onto current `main` (`05fbfb8`) |
| Code written | None — documentation only |
| Blockers | Founder hardware inventory (PHASE0-SETUP §7 FOUNDER-TBD) |

---

## 2. Completed Milestones (chronological)

### ✅ M-01 — Strategic plan locked (pre-session)

- `docs/MASTER-PLAN.md` v1.0.0 — Tier-1 audited, `APPROVED FOR /plan`.
- Predecessors archived in `docs/_archive/` (AUDIT-REPORT-V1, QUALITY-GATE-REPORT-v1).
- **Commit:** `2996bf5` (main).

### ✅ M-02 — Tier-1 documentation foundation

Commit `49ba49a` adds **55 files / ~7,200 lines**:

- **Root:** `CLAUDE.md`, `.gitignore`, `.editorconfig`, `.nvmrc`.
- **Architecture decisions:** `docs/adr/` — 20 ADRs covering every technology + strategy decision (ADR-001..019 from Master Plan §3; ADR-020 for Validated Hybrid strategy).
- **Design / UI-UX:** `docs/design/` — 10 files (DESIGN-SYSTEM, SCREENS-INVENTORY, USER-JOURNEY-MAP, INFORMATION-ARCHITECTURE, RESPONSIVE-STRATEGY, INTERACTION-PATTERNS, ACCESSIBILITY, UX-COPY, WIREFRAMES, BRAND).
- **Product:** `docs/product/` — PRD, USER-STORIES, FEATURE-FLAGS.
- **Operations:** `docs/operations/` — SLO, IR-PLAN, DR.
- **Security detail:** `docs/security/` — THREAT-MODEL (Week-4 scaffold), CSP (full policy).
- **Performance:** `docs/performance/BUDGET.md`.
- **Architecture detail:** `docs/architecture/` — API-CONTRACT, SCHEMA, STATE-SYNC.
- **Content/Legal:** `docs/content/PIPELINE.md`, `docs/legal/REPERTOIRE-PROVENANCE.md`.
- **Risk:** `docs/risks/REGISTER.md`.
- **Process:** `docs/ARCHITECTURE.md`, `docs/CONTRIBUTING.md`, `docs/SECURITY.md`, `docs/README.md`.

### ✅ M-03 — Rebase onto current main

- Rebased `49ba49a` onto `origin/main` (`05fbfb8`); one `.gitignore` conflict resolved by merging both rule sets.
- Result: linear history; Devran AI Kit tooling dirs now correctly ignored.

### ✅ M-04 — Phase-0 Day-1 ambiguities resolved

Commit `3224091` adds **9 files / 1,405 lines**:

- **`docs/decisions/PHASE0-SETUP.md`** — 8 decisions locked (Senior Staff Engineer authority).
- **`docs/decisions/G1-DECISION.md`** — Week-3 gate decision template.
- **`docs/research/INTERVIEWS-PHASE0/`** — CONSENT-FORM (TR+EN), INTERVIEW-GUIDE (JTBD), OUTREACH-TEMPLATES (10 variants), RAW/ placeholder (gitignored contents).
- **`docs/sprint0/`** — LANDING-COPY (TR+EN), AD-CREATIVES-BRIEF (5 Meta + 3 Google ad groups).
- **`.gitignore`** — refined RAW/ pattern: contents ignored, `.gitkeep` tracked.

---

## 3. Pending Items — Prioritised

### 🔴 P0 — Blockers (must resolve before Week 1 Day 1)

1. **Founder: confirm hardware inventory** → update `docs/decisions/PHASE0-SETUP.md §7` from `FOUNDER-TBD` to `CONFIRMED`.
   - Required minimum: macOS + Windows + iOS Safari + Android Chrome + wired USB MIDI.
   - Any gap → either borrow / BrowserStack / purchase within 3 days, OR document as G1 scope limitation.

### 🟠 P1 — Week 1 Day 1–2 setup (founder + AI co-execution)

In rough ordering (some parallel):

2. **Founder accounts (operational):**
   - MailerLite EU account + API key → store as `MAILERLITE_API_KEY` in `.env.local` (gitignored).
   - Plausible EU account + site registration.
   - Whereby workspace + permanent room URL.
   - Cloudflare account (if not existing) + hardware MFA (YubiKey).
   - Meta Business Manager + Google Ads account creation (**critical path** per P0-R1 — start Day 1).
   - Wise Business account (pedagogue payments).
   - Domain registration + email forwarding if not yet done.

3. **AI scaffolding (can start while founder handles accounts):**
   - Initialise `apps/sprint0/` (Vite + React 18 + TS strict) — landing page skeleton.
   - Cloudflare Pages `_headers` file with CSP (per `docs/security/CSP.md`).
   - Wire MailerLite API + Plausible events for landing form.
   - Implement landing per `docs/sprint0/LANDING-COPY.md`.
   - First Playwright E2E: landing → email submit → success state.
   - Initialise `prototype/` (Vite + React + TS strict) — 2-piece vertical slice scaffold.
   - TDD modules: `latency-probe.ts`, `score-follower/follower.ts`, `score-follower/chord-window.ts`, `license/validator.ts`.

4. **Source 2 MusicXML for prototype:**
   - `prototype/corpus/bwv772.musicxml` — re-engraved or IMSLP-verified PD source.
   - `prototype/corpus/cmaj-scale.musicxml` — hand-authored in MuseScore 4.

5. **Send outreach (founder, Day 1–2):**
   - 20 interview invitations (5 TR teachers, 5 DE teachers, 5 self-learners, 5 advanced) using `OUTREACH-TEMPLATES.md`.
   - 15 pedagogue cold messages (LinkedIn + EPTA + personal).

### 🟡 P2 — Week 1 Day 3–7

6. **Ad creative production** per `docs/sprint0/AD-CREATIVES-BRIEF.md` (5 Meta + 3 Google groups, UTM tagged).
7. **Phase-0 privacy notice / terms / a11y pages** at `/sprint0/privacy`, `/sprint0/terms`, `/sprint0/accessibility`.
8. **Landing deployed to Cloudflare Pages production domain** (per P0-R9: ads point to production, not preview).

### 🟢 P3 — Week 2

9. Run ads (7 days).
10. Conduct 8 interviews.
11. Conduct 1–2 pedagogue consultations.
12. Prototype: OSMD integration, Tone.js audio, WebMIDI bridge, latency probe, score-follower.

### 🟢 P4 — Week 3

13. Conduct remaining 7 interviews.
14. Complete 3 pedagogue consultations.
15. Cross-device prototype test matrix (`prototype/TEST-RESULTS.md`).
16. Synthesis: `docs/research/PHASE0-RESULTS.md`.
17. **G1 decision meeting** — fill `docs/decisions/G1-DECISION.md`.
18. Commit G1 decision to main.

### ⏳ Deferred (post-G1, conditional on PASS)

19. Register TR şahıs şirketi (legal entity decision, PHASE0-SETUP §1).
20. Initialise Phase-1 repo structure: `apps/web/`, `apps/worker/`, `corpus/`, `.github/workflows/`.
21. STRIDE threat model workshop (Week 4, first task of Phase 1).
22. Stripe Checkout + webhook wiring.
23. Full Salamander Lite sample set build.
24. Complete 15 Bach Inventions engraving + content QA pipeline runs.
25. Legal: KVKK Aydınlatma Metni + GDPR privacy policy + ToS + subprocessor list, counsel-reviewed.
26. CI pipeline (GitHub Actions per ADR-016).
27. Sentry + Honeycomb + PostHog integrations.

---

## 4. Open Questions (flag to resolve later)

- **SRS rating in Phase 1 summary screen:** capture now to seed Phase-2 FSRS, or defer entirely? (PRD §7)
- **Default fingering layer:** AI (Parncutt) or Editorial (Czerny)? (resolve via Phase-0 prototype user-test)
- **Studio lane gating:** default-on for all Phase 1 users, or gated by flag until G1 signal? (FEATURE-FLAGS catalogue)
- **Visual-regression testing tool:** Chromatic free tier vs Playwright snapshots — decide at Phase-1 Week 4.
- **Testing framework specifics:** `format-message` vs `@formatjs/intl` for ICU pluralisation — Week 4.

---

## 5. How to Resume (for any future session)

1. **Read `CLAUDE.md`** at repo root — orientation + Hard NO list.
2. **Read `docs/MASTER-PLAN.md`** — strategic authority.
3. **Read this `docs/PROGRESS.md`** — current state + prioritised next actions.
4. **Check** `git log --oneline -10` — recent commits.
5. **Check** `docs/decisions/*` — look for unresolved `FOUNDER-TBD` or `PROPOSED` ADRs.
6. **Pick the topmost P0 / P1 item** and confirm with the user before starting.

---

## 6. Session Log

| Date | Session summary | Commits | Progress delta |
|---|---|---|---|
| 2026-04-17 | Master Plan v1.0.0 authored | `2996bf5` | M-01 |
| 2026-04-22 | Tier-1 foundation (CLAUDE.md + ADRs + design/ops/product specs) | `49ba49a` | M-02 |
| 2026-04-22 | Rebased onto main; `.gitignore` conflict resolved | — | M-03 |
| 2026-04-22 | Phase-0 Day-1 ambiguities + Week-1 artefacts | `3224091` | M-04 |
| 2026-04-22 | Progress log + PR preparation | (this commit) | — |

New rows appended per session.

---

## 7. References

- [`MASTER-PLAN.md`](MASTER-PLAN.md) — strategic authority
- [`CLAUDE.md`](../CLAUDE.md) — session orientation
- [`decisions/PHASE0-SETUP.md`](decisions/PHASE0-SETUP.md) — Day-1 decisions
- [`decisions/G1-DECISION.md`](decisions/G1-DECISION.md) — Week-3 gate template
- [`adr/README.md`](adr/README.md) — 20 ADRs index
- [`README.md`](README.md) — docs index

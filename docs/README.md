# docs/ — Piano-Practice Documentation Index

> **Single source of truth:** [`MASTER-PLAN.md`](MASTER-PLAN.md).
> If any document diverges from the Master Plan, the Master Plan wins and the other is stale.

---

## Map

### Strategic

- [`MASTER-PLAN.md`](MASTER-PLAN.md) — Tier-1 audited product, strategy, roadmap, gates
- [`_archive/`](_archive/) — superseded predecessors (QUALITY-GATE-REPORT, AUDIT-REPORT)

### Architecture

- [`ARCHITECTURE.md`](ARCHITECTURE.md) — system overview, diagrams, data flow, trust boundaries
- [`adr/`](adr/) — ADR-001 through ADR-020 (technology + strategy decisions)
- [`architecture/API-CONTRACT.md`](architecture/API-CONTRACT.md) — Worker endpoints, license token, export schema
- [`architecture/SCHEMA.md`](architecture/SCHEMA.md) — Dexie + Postgres schema
- [`architecture/STATE-SYNC.md`](architecture/STATE-SYNC.md) — sync model per phase

### Product

- [`product/PRD.md`](product/PRD.md) — feature-level requirements (Phase 1)
- [`product/USER-STORIES.md`](product/USER-STORIES.md) — stories with acceptance criteria
- [`product/FEATURE-FLAGS.md`](product/FEATURE-FLAGS.md) — PostHog flag catalogue

### Design (UI / UX)

- [`design/DESIGN-SYSTEM.md`](design/DESIGN-SYSTEM.md) — tokens, components, a11y baseline
- [`design/SCREENS-INVENTORY.md`](design/SCREENS-INVENTORY.md) — every screen, every state
- [`design/USER-JOURNEY-MAP.md`](design/USER-JOURNEY-MAP.md) — Phase 1 flows
- [`design/INFORMATION-ARCHITECTURE.md`](design/INFORMATION-ARCHITECTURE.md) — site map + routing
- [`design/RESPONSIVE-STRATEGY.md`](design/RESPONSIVE-STRATEGY.md) — mobile + web + PWA
- [`design/INTERACTION-PATTERNS.md`](design/INTERACTION-PATTERNS.md) — MIDI, notation, practice UX
- [`design/ACCESSIBILITY.md`](design/ACCESSIBILITY.md) — WCAG 2.1 AA
- [`design/UX-COPY.md`](design/UX-COPY.md) — voice, errors, empty states (TR + EN)
- [`design/WIREFRAMES.md`](design/WIREFRAMES.md) — low-fidelity ASCII wireframes
- [`design/BRAND.md`](design/BRAND.md) — positioning, voice, logo principles

### Security

- [`SECURITY.md`](SECURITY.md) — security posture overview
- [`security/THREAT-MODEL.md`](security/THREAT-MODEL.md) — STRIDE (Week-4 workshop seed)
- [`security/CSP.md`](security/CSP.md) — full Content Security Policy

### Operations

- [`operations/SLO.md`](operations/SLO.md) — service-level objectives + burn-rate alerts
- [`operations/IR-PLAN.md`](operations/IR-PLAN.md) — incident response + severity matrix
- [`operations/DR.md`](operations/DR.md) — disaster recovery + business continuity

### Performance

- [`performance/BUDGET.md`](performance/BUDGET.md) — performance budget + CI gates

### Content / Legal

- [`content/PIPELINE.md`](content/PIPELINE.md) — repertoire QA pipeline
- [`legal/REPERTOIRE-PROVENANCE.md`](legal/REPERTOIRE-PROVENANCE.md) — PD status per piece per jurisdiction

### Risk

- [`risks/REGISTER.md`](risks/REGISTER.md) — live risk register

### Process

- [`CONTRIBUTING.md`](CONTRIBUTING.md) — branching, commits, PRs, CI gates, review

### Phase outputs (to be produced during execution)

- `research/INTERVIEWS-PHASE0/` — Phase-0 customer discovery (RAW/ gitignored)
- `research/PHASE0-RESULTS.md` — Phase-0 synthesis
- `decisions/PHASE0-SETUP.md` — Day-1 ambiguity resolutions
- `decisions/G1-DECISION.md` — formal Gate G1 decision
- `decisions/G2-DECISION.md`, `G3-DECISION.md` — subsequent gates
- `operations/incidents/` — SEV-1 / SEV-2 post-mortems
- `operations/tabletops/` — scheduled tabletop exercises
- `operations/dr-tests/` — DR test results
- `performance/BUDGET-OVERRIDES.md` — append-only override log

---

## Reading Order for a New Contributor (human or AI)

1. [`../CLAUDE.md`](../CLAUDE.md) — orientation + Hard NO list
2. [`MASTER-PLAN.md`](MASTER-PLAN.md) — strategy, scope, gates
3. [`ARCHITECTURE.md`](ARCHITECTURE.md) — how it fits together
4. [`adr/README.md`](adr/README.md) — why each piece is the way it is
5. [`design/SCREENS-INVENTORY.md`](design/SCREENS-INVENTORY.md) — what the user sees
6. [`product/PRD.md`](product/PRD.md) — what we're building
7. [`SECURITY.md`](SECURITY.md) — non-negotiables
8. [`CONTRIBUTING.md`](CONTRIBUTING.md) — how to ship

---

## Amendment Policy

- **Master Plan** amendments are rare and go through the `quality-gate` workflow rigor. Do not silently edit.
- **ADRs** supersede; never edit historical ADRs in place.
- **All other docs** may be updated via regular PR; each PR states which Master Plan / ADR it references.

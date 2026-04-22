# CLAUDE.md — Piano-Practice Project Guide

> **Audience:** Every Claude Code session (and any AI agent) working in this repo.
> **Authority:** [docs/MASTER-PLAN.md](docs/MASTER-PLAN.md) — Tier-1 audited, `v1.0.0`, **APPROVED FOR `/plan`**.
> **Rule #1:** If anything in this file conflicts with `MASTER-PLAN.md`, the Master Plan wins. Surface the conflict, do not silently resolve it.

---

## 1. What This Project Is

A **browser-first, offline-capable piano practice platform** bridging absolute beginners and serious amateur/pre-professional pianists. Not a toy, not a gamified cartoon — real engraved notation, real sampled piano, real MIDI, pedagogically accurate score-following, and an AI fingering layer trained exclusively on confirmed public-domain corpora.

Phase-1 (MVP) scope is deliberately narrow: **15 Bach Two-Part Inventions (BWV 772–786)** sold as a $29 lifetime license. Everything else is gated behind validation gates G1, G2, G3. See `MASTER-PLAN.md §2`.

The existing `index.html` + `README.md` at the repo root represent the **pre-plan v0 prototype** ("Piyano Stüdyosu — Görsel Pratik"). They are preserved for historical reference and the public GitHub Pages demo. **New development follows the Master Plan from scratch.**

---

## 2. Stack (Locked — see `docs/adr/`)

| Layer | Choice | ADR |
|---|---|---|
| Language | TypeScript (strict) | ADR-001 |
| Framework | React 18 | ADR-001 |
| Build | Vite | ADR-001 |
| Notation | OpenSheetMusicDisplay (OSMD) | ADR-002 |
| Audio | Tone.js + `@tonejs/piano` + Salamander Grand V3 | ADR-003 |
| MIDI | WebMidi.js (input), `@tonejs/midi` (file parse) | ADR-004 |
| Fingering | `pianoplayer` (Parncutt 1997), build-time only | ADR-005 |
| State | Zustand (UI) + Dexie (IndexedDB) | ADR-006 |
| PWA | Workbox | ADR-007 |
| Hosting | Cloudflare Pages + R2 + Workers | ADR-008 |
| Backend (Phase 2+) | Supabase EU region | ADR-009 |
| Payments | Stripe Checkout (SAQ-A, hosted) | ADR-010 |
| SRS (Phase 2+) | `ts-fsrs` | ADR-012 |
| Observability | Sentry + OpenTelemetry → Honeycomb | ADR-015 |
| CI/CD | GitHub Actions + Cloudflare Pages previews | ADR-016 |

**Node version:** pinned in `.nvmrc`. **Package manager:** npm (lockfile-committed). Do not introduce Yarn/pnpm without an ADR.

---

## 3. Commands (Placeholders — to be filled during Phase 1 bootstrap)

```bash
# Not yet scaffolded. During Phase-0 prototype (Week 1-3) and Phase-1 bootstrap (Week 4):
npm install
npm run dev          # Vite dev server
npm run build        # Production build
npm run preview      # Preview production build
npm run typecheck    # tsc --noEmit (strict)
npm run lint         # ESLint
npm run test         # Vitest
npm run test:e2e     # Playwright
npm run test:cov     # Coverage (≥80% critical paths)
```

**Before you invent commands:** check `package.json`. If missing, surface it — do not fabricate.

---

## 4. File Structure (Target — emerges during Phase 1)

```
piano-practice/
├── CLAUDE.md                   ← this file
├── README.md                   ← public README (v0 prototype, preserve)
├── index.html                  ← v0 prototype (preserve, do not modify)
├── .gitignore .editorconfig .nvmrc
├── .github/workflows/          ← CI (ADR-016)
├── apps/
│   ├── web/                    ← React + Vite SPA
│   └── worker/                 ← Cloudflare Worker (Stripe webhook, license issuance)
├── corpus/                     ← Bundled MusicXML, MIDI, fingering manifests (PD only)
│   └── metadata.json           ← cryptographic hash per piece (CI verifies)
├── docs/
│   ├── MASTER-PLAN.md          ← single source of truth
│   ├── ARCHITECTURE.md
│   ├── CONTRIBUTING.md
│   ├── SECURITY.md
│   ├── adr/                    ← ADR-001..ADR-019
│   ├── operations/             ← SLO, DR, IR-PLAN
│   ├── security/               ← THREAT-MODEL, CSP, IR-TEMPLATES
│   ├── content/                ← PIPELINE, REPERTOIRE-PROVENANCE
│   ├── research/               ← Phase-0 findings
│   ├── decisions/              ← G1/G2/G3 decision records
│   └── _archive/               ← historical (QUALITY-GATE-REPORT, AUDIT v1)
└── prototype/                  ← Phase-0 2-piece vertical slice (disposable)
```

Conventions:
- **Many small files > few large files.** 200–400 lines typical, 800 max.
- **Feature-first** organization, not type-first.
- `src/` inside each app is split by domain: `notation/`, `audio/`, `midi/`, `score-follower/`, `license/`, `practice/`, `settings/`, `db/`.

---

## 5. Coding Standards

- **TypeScript strict mode.** No `any`, no `@ts-ignore` without a comment citing the reason.
- **Immutability.** Never mutate; always return new objects (`{...obj, field}`). See `~/.claude/rules/coding-style.md`.
- **Zod validation at every boundary** (user input, network, storage deserialization). Internal trust is fine; boundaries are not.
- **No `console.log` in committed code.** Use a scoped logger or Sentry breadcrumb.
- **No hardcoded secrets.** Use Cloudflare Workers Secrets; `.env.local` is gitignored.
- **No default exports** for modules (named exports only) — easier refactor + better tree-shake.
- **Error handling at boundaries**; within a trust domain, let errors propagate to a single handler.
- **Accessibility is not optional.** WCAG 2.1 AA baseline. Lighthouse a11y = 100 in CI.

---

## 6. Testing Strategy

- **TDD for all new code.** RED → GREEN → REFACTOR. Use the `tdd-guide` agent.
- **Unit (Vitest):** score-follower, audio engine, MIDI handler, license validator, fingering manifest loader. **≥80% line coverage** on these critical paths.
- **Integration:** Cloudflare Worker webhook handler against Stripe test fixtures; Dexie migrations forward+back.
- **E2E (Playwright):** load → select piece → play with virtual MIDI → completion; Stripe Checkout happy path against test keys.
- **Accessibility (CI):** Lighthouse = 100, axe-core zero violations on critical screens.
- **Performance budget (CI):** see Master Plan §4.3 — initial JS ≤ 200kb gz, LCP p75 ≤ 2.5s, audio latency p95 ≤ 30ms wired MIDI.

---

## 7. Security Rules (non-negotiable)

See [docs/SECURITY.md](docs/SECURITY.md) and `MASTER-PLAN.md §5`.

- **16+ age gate only.** No children pathway. No COPPA. (ADR-017)
- **TR + EU geo only.** US, UK, BR deferred — do not add region code for them. (ADR-018)
- **No user uploads in Phase 1.** Only bundled PD corpus. (§5.3)
- **Stripe Checkout hosted only.** No Elements. SAQ-A scope must stay SAQ-A. (§5.5)
- **RLS-by-default** when Supabase is introduced Phase 2+. Every table, no exceptions. (§5.6)
- **Never commit secrets.** `gitleaks` runs pre-commit and in CI.
- **CSP in Report-Only for 2 weeks before enforce.** Do not flip to enforce without the monitoring window.

---

## 8. Hard NO List (AI agents: read carefully)

These are decided. Do not re-open without explicit owner override + ADR amendment.

1. **Do not target users under 16.** No COPPA flow, no family tier, no "for kids" variant in Phase 1/2.
2. **Do not launch in US, UK, or BR.** Geo-fence features that require it. (ADR-018)
3. **Do not add multi-device sync in v1.0.** Device-local Dexie only; JSON export/import is the portability story. Yjs/CRDT is Phase 3 (ADR-013, ADR-014).
4. **Do not introduce user accounts in Phase 1.** License key via Stripe email receipt is the entire auth story. (ADR-019)
5. **Do not accept user-uploaded MusicXML/MIDI in Phase 1.** Zero upload surface until the full pipeline in §5.3 exists.
6. **Do not add repertoire outside the approved PD corpus.** Any addition requires a row in `docs/legal/REPERTOIRE-PROVENANCE.md` with PD verification per jurisdiction.
7. **Do not collect children's data, biometrics, or precise geolocation.** Ever, at any phase, without ADR amendment.
8. **Do not ship runtime ML / personalization without a DPIA.** `pianoplayer` build-time fingering is fine; anything per-user is Phase 3+ only.
9. **Do not use Stripe Elements.** Checkout (hosted) only — this is what keeps PCI scope at SAQ-A.
10. **Do not skip the CSP Report-Only phase.** Two weeks minimum before enforce.
11. **Do not change the phase/gate timeline casually.** Master Plan timeline is 52 weeks Y1. Re-planning requires a gate re-run.
12. **Do not touch `index.html` or `README.md` at the repo root.** They are the v0 prototype artefact for the GitHub Pages demo.

---

## 9. Decision Gates (where the project can stop)

See `MASTER-PLAN.md §11`.

- **G1 (Week 3):** CAC ≤ €15, intent ≥ 5%, audio latency p95 ≤ 50ms, 10/15 interviews validate. Fail → pivot or sunset.
- **G2 (Week 20, 8 weeks post-launch):** ≥100 paying users at $29, refund <10%, NPS ≥ 30. Fail → root-cause then decide.
- **G3 (Week 24):** ≥250 cumulative customers, ≥3% conversion, ≥30% week-4 retention. Fail → harvest as side-project, defer Phase 3.

Every non-trivial PR should note which phase/gate it serves.

---

## 10. Agent Orchestration (when Claude delegates)

Follow `~/.claude/rules/agents.md`. Especially useful here:

- **planner** — for Phase 0/1 task breakdowns from Master Plan.
- **tdd-guide** — for every new module (write tests first).
- **code-reviewer** — after every material change.
- **security-reviewer** — before every commit touching auth, webhook, license, CSP, upload pipeline, or secrets.
- **architect** — only for proposals to amend an ADR.

**Parallelize** independent agent tasks. Do not serialize work that has no dependency.

---

## 11. Commits, Branches, PRs

See [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md).

Summary:
- Branch: `feat/<scope>`, `fix/<scope>`, `refactor/<scope>`, `docs/<scope>`, `chore/<scope>`.
- Commit subject: `<type>: <imperative, <72 chars>`.
- Conventional types: `feat | fix | refactor | docs | test | chore | perf | ci`.
- No co-author attribution (globally disabled via `~/.claude/settings.json`).
- Every PR: linked gate/phase, risk level, test plan, rollback plan.

---

## 12. Cross-References

- Strategy, phases, gates, budgets → [docs/MASTER-PLAN.md](docs/MASTER-PLAN.md)
- Architecture, data flow, trust boundaries → [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)
- Every major technical/strategic decision → [docs/adr/](docs/adr/)
- Security posture, threat model, CSP, IR → [docs/SECURITY.md](docs/SECURITY.md)
- How to contribute (even solo) → [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md)
- Historical context (audit findings, v1.0 plan) → `docs/_archive/`

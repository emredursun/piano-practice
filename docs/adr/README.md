# Architecture Decision Records

> **Purpose:** Every significant technical or strategic decision gets an ADR. The Master Plan is the *why* at the portfolio level; ADRs are the *why* per decision, in a compact auditable form.
> **Source of truth alignment:** IDs, rationale, and status in this directory match [`../MASTER-PLAN.md §3`](../MASTER-PLAN.md) one-for-one.

## Status Legend

- **Proposed** — under discussion
- **Accepted** — current policy
- **Deferred** — accepted in principle, not yet in force (typically gated to a future phase)
- **Superseded by ADR-NNN** — no longer current; pointer to its replacement
- **Deprecated** — no longer current, no replacement

## Index

| ID | Decision | Status |
|---|---|---|
| [ADR-001](ADR-001-frontend-stack.md) | Frontend: React 18 + Vite + TypeScript strict | Accepted |
| [ADR-002](ADR-002-notation-osmd.md) | Notation: OpenSheetMusicDisplay (OSMD) | Accepted |
| [ADR-003](ADR-003-audio-engine.md) | Audio: Tone.js + @tonejs/piano + Salamander Grand V3 | Accepted |
| [ADR-004](ADR-004-midi.md) | MIDI: WebMidi.js + @tonejs/midi | Accepted |
| [ADR-005](ADR-005-fingering.md) | Fingering: pianoplayer (Parncutt 1997) at build-time | Accepted |
| [ADR-006](ADR-006-state-persistence.md) | State: Zustand + Dexie (IndexedDB) | Accepted |
| [ADR-007](ADR-007-build-pwa.md) | Build/PWA: Vite + Workbox | Accepted |
| [ADR-008](ADR-008-hosting.md) | Hosting: Cloudflare Pages + R2 + Workers | Accepted |
| [ADR-009](ADR-009-backend-supabase.md) | Backend (Phase 2+): Supabase EU region | Accepted (deferred to Phase 2) |
| [ADR-010](ADR-010-payments-stripe.md) | Payments: Stripe Checkout (SAQ-A) | Accepted |
| [ADR-011](ADR-011-repertoire-phase1.md) | Repertoire (Phase 1): Bach Inventions, PD 1853 edition | Accepted |
| [ADR-012](ADR-012-srs-fsrs.md) | SRS: ts-fsrs (FSRS-4.5) | Accepted (Phase 2+) |
| [ADR-013](ADR-013-sync-v1-device-local.md) | Sync v1.0: device-local only | Accepted |
| [ADR-014](ADR-014-sync-v2-crdt.md) | Sync v2.0+: Yjs CRDT over Supabase Realtime | Accepted (deferred) |
| [ADR-015](ADR-015-observability.md) | Observability: Sentry + OpenTelemetry → Honeycomb | Accepted |
| [ADR-016](ADR-016-cicd.md) | CI/CD: GitHub Actions + Cloudflare Pages previews | Accepted |
| [ADR-017](ADR-017-age-policy.md) | Children's data policy: 16+ hard age gate | Accepted |
| [ADR-018](ADR-018-geo-scope.md) | Geo scope v1.0: TR + EU only | Accepted |
| [ADR-019](ADR-019-license-model.md) | License model v1.0: $29 lifetime perpetual (license key) | Accepted |
| [ADR-020](ADR-020-validated-hybrid-strategy.md) | Strategy: Validated Hybrid (Option C → B → A) | Accepted |

> Note: ADR-020 was requested by the onboarding scope as "Validated Hybrid strategy". The Master Plan index (§3) lists 19 technology ADRs; the strategic path (§2) is codified here as ADR-020 so the strategy decision has the same formal status as the technology decisions.

## Template

New ADRs start from [`ADR-TEMPLATE.md`](ADR-TEMPLATE.md). Copy, renumber, keep under ~400 words.

## Amendment Process

1. Open a new ADR that supersedes the old one (never edit the old ADR's decision in place; add a "Superseded by ADR-NNN" header).
2. PR includes: motivation, what changed, migration plan, affected files.
3. Update [`../MASTER-PLAN.md §3`](../MASTER-PLAN.md) index and this README's table.
4. Label with the phase in which the amendment takes effect.

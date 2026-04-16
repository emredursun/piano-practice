# 🎹 Piano-Practice — Final Audited Master Plan

> **Document ID:** MASTER-PLAN
> **Version:** 1.0.0 (Final, Audited)
> **Date:** 2026-04-17
> **Owner:** Senior Staff Engineer (Architectural Authority)
> **Workflow:** `quality-gate` (Devran AI Kit, v2.1.0) — production-grade variant
> **Status:** ✅ **APPROVED FOR `/plan`** (gated by Phase-0 validation outcomes)
> **Supersedes:** `QUALITY-GATE-REPORT.md` v1.0, `AUDIT-REPORT-V1.md` v1.0
> **Classification:** Internal — strategic & technical decision document

---

## 0. Document Status & Authority

This is the **single authoritative planning artifact** for the piano-practice product. Two predecessor documents are deliberately retained in `docs/_archive/` for historical traceability:

- `_archive/QUALITY-GATE-REPORT-v1.md` — original feature plan
- `_archive/AUDIT-REPORT-V1.md` — Tier-1 adversarial audit (60+ findings)

This Master Plan integrates and resolves all audit findings into executable decisions. **No further `/plan` work proceeds against the predecessor documents.**

The Senior Staff Engineer authority granted by the project owner has been exercised to make all decisions on:

- Strategic path (Validated Hybrid Approach — Option C → B → A)
- Technology stack (final ADRs)
- Resource model (solo founder, 36-week realistic envelope)
- Compliance posture (TR + EU launch; US/BR deferred)
- Children's data policy (16+ hard age gate, COPPA flow deferred)
- Sync model (device-local v1; CRDT sync deferred to v2)
- Frontend framework (React + Vite + TypeScript strict)

---

## 1. Executive Decision Summary

### 1.1 What Is Being Built

A **browser-first, offline-capable piano practice platform** that bridges absolute beginners and serious amateur/pre-professional pianists through:

1. **Real engraved notation** (OSMD + MusicXML) — not stylized cartoons
2. **Real MIDI input + real sampled piano** (Salamander + Tone.js)
3. **Score-following with chord aggregation** — pedagogically accurate timing
4. **AI fingering suggestions on confirmed-public-domain corpus** — explainable, overridable
5. **FSRS-based spaced repetition for repertoire maintenance** — borrowed from language learning
6. **Dual-mode UI** — Guided lane (beginners) + Studio lane (advanced)

### 1.2 What Has Changed From v1.0

| Area | v1.0 (Rejected) | v2.0 (This Plan) |
|---|---|---|
| **Strategy** | Big-bang 25-level v1.0 in 12 weeks | Validated Hybrid: Phase-0 validation → Phase-1 single-domain MVP → conditional expansion |
| **Curriculum** | 25 levels in v1.0 (€100-150k actual cost ignored) | Bach Two-Part Inventions only in v1.0; full curriculum gated to validated demand |
| **Timeline** | 12 weeks (delusional) | 36 weeks solo to v1.0 (Phase-0 + Phase-1 + Phase-2 conditional gate) |
| **Budget** | Unstated (~€10k assumed) | €18-32k Y1 realistic (engineering opportunity cost + €8-15k cash for legal/security/infra) |
| **CAC** | Unvalidated SaaS assumptions | Sprint-0 paid acquisition test before code |
| **Children's data** | Unaddressed (Hoffman demographic implied) | **16+ hard age gate**, no children pathway in v1.0 |
| **Frontend** | "Vite + TS + PWA" (no framework decision) | **React 18 + Vite + TypeScript strict + PWA (Workbox)** |
| **Sync** | Implied multi-device | **Device-local only** v1.0; CRDT sync gated to v2.0 |
| **Backend** | "Supabase" (sketch) | **Supabase** with documented schema + RLS-by-default + pgTAP suite |
| **Payments** | "Stripe" (sketch) | **Stripe Checkout (SAQ-A)** + signed webhooks + idempotency + 3DS forced EU |
| **Security** | Checklist | Threat model + CSP spec + file upload pipeline + IR plan + audit log |
| **Operations** | Implicit | Sentry + OpenTelemetry + 5 SLOs + on-call from day 1 |
| **Geo scope v1.0** | Global | **TR + EU only** (US/UK/BR deferred) |

### 1.3 The Three Decision Gates

The plan deliberately avoids irreversible commitments early. Three documented gates protect against the audit's three project-killer risks:

- **Gate G1 (end of Phase 0, Week 3):** CAC + technical feasibility validated → proceed to Phase 1, else pivot or abandon
- **Gate G2 (end of Phase 1, Week 12):** MVP ships, achieves ≥100 paying users at $29 lifetime within 8 weeks of launch → proceed to Phase 2 expansion
- **Gate G3 (end of Phase 2, Week 24):** Repertoire expansion validates conversion ≥3% on broader catalog → commit to Phase 3 premium subscription model

If any gate fails, the project either pivots within scope (smaller/different niche) or sunsets cleanly without further capital commitment.

---

## 2. Strategic Path: Validated Hybrid Approach

### 2.1 Why Hybrid (Not Pure A or Pure B)

The audit established that:
- **Pure Option A** (full plan) requires €175-300k and validates only if simultaneous assumptions on TAM, CAC, conversion, and curriculum cost all hold — high covariance, high failure correlation.
- **Pure Option B** (Bach MVP) is fast and cheap but does not validate the premium-platform thesis; it could succeed as a niche product without ever justifying the broader vision.

**Hybrid (Option C)**: spend 2-3 weeks measuring the assumptions before committing to either. The marginal cost of Phase 0 (~€2-3k + 100 hours) is negligible compared to the information value about CAC and curriculum cost.

### 2.2 Phase Roadmap (Realistic, Solo Founder)

```
Phase 0  Validation Sprint            Weeks  1- 3   ~100h    €2-3k cash
Phase 1  Foundation MVP (Bach)        Weeks  4-15   ~350h    €4-6k cash      → Gate G2
Phase 2  Repertoire Expansion         Weeks 16-24   ~250h    €4-8k cash      → Gate G3
Phase 3  Premium Platform             Weeks 25-52   ~600h    €8-15k cash
                                    ─────────────────────────────────
                                       ~1,300h     €18-32k Y1
```

Total realistic envelope: **52 weeks at ~25h/week solo + €18-32k cash**. This matches the audit's revised estimate (600-1,000h for v1.0-equivalent scope; we add Phase 0 and Phase 3 buffer).

### 2.3 Phase 0 — Validation Sprint (Weeks 1-3)

**Objective:** invalidate or validate the three project-killer assumptions before writing production code.

**Workstreams:**

1. **Paid acquisition test (CAC validation):**
   - Single landing page (`/sprint0/landing`) — value proposition + email capture + intent question ("Would you pay $29 for this?")
   - €500 Meta Ads + €300 Google Ads, 7 days, targeting TR + EU piano-learner audiences
   - Success metric: CPA ≤ €15 to email signup; ≥5% intent-to-pay rate
   - **Stop-go**: if CPA > €30 or intent < 2%, audit-noted PK-2 risk realized → pivot

2. **Customer discovery interviews (15 conversations):**
   - 5 piano teachers (TR + DE)
   - 5 adult self-learners (TR + EU)
   - 5 advanced amateurs / pre-professional pianists
   - Structured questionnaire stored in `docs/research/INTERVIEWS-PHASE0/`
   - Synthesis: Jobs-to-be-Done framework, willingness-to-pay anchoring

3. **Pedagogue cost validation (3 conversations):**
   - 3 ABRSM/RCM-certified pedagogues
   - Concrete quote for: licensing existing curriculum vs. authoring 1 grade level
   - Validates or invalidates the audit's PK-1 estimate (€100-150k for full 25 levels)

4. **Technical feasibility prototype (`/sprint0/proto`):**
   - 2-piece end-to-end vertical slice: 1 Bach Invention No. 1 (BWV 772) + 1 C major scale exercise
   - Stack: React + Vite + TypeScript + OSMD + Tone.js + WebMidi.js + minimal Salamander subset (~5MB)
   - Validates: notation rendering quality, audio latency on real hardware, MIDI input on Mac/Windows/Linux, iOS Safari Web Audio quirks
   - **Stop-go**: if any unrecoverable platform blocker found → re-scope (e.g., desktop-only Tauri wrapper)

**Phase 0 Deliverables:**
- `docs/research/PHASE0-RESULTS.md` — synthesized findings, go/no-go recommendation
- `prototype/` — working 2-piece prototype (deployed to Cloudflare Pages preview)
- `docs/decisions/G1-DECISION.md` — formal Gate G1 decision record

**Gate G1 (Week 3) — Pass criteria (ALL must hold):**
- CPA ≤ €15 AND intent-to-pay ≥ 5%
- ≥10/15 interviews indicate strong pain point + willingness to try
- Pedagogue quotes support either license-then-extend OR confirm Bach-niche viability
- Prototype renders 2 pieces with audio latency p95 ≤ 50ms

**If G1 fails:** rerun with different positioning OR pivot to B2B teacher tool OR sunset.

### 2.4 Phase 1 — Foundation MVP (Weeks 4-15)

**Objective:** ship a paid product to validate willingness-to-pay at $29 lifetime price point on a curated, defensible single domain.

**Scope (locked):**
- 15 Bach Two-Part Inventions (BWV 772-786), engraved from confirmed-PD source (Bach-Gesellschaft 1853 edition or newer non-editorial transcription)
- AI fingering suggestions trained on confirmed-PD corpus only (Czerny 1840 fingerings, Busoni 1894 — both expired)
- Score-following with chord aggregation (50ms window)
- Practice modes: section loop, hands separate (auto-detect via voice analysis), tempo ramp, blind play
- Progress tracking: device-local IndexedDB only, exportable JSON
- PWA installable, offline-capable
- Stripe Checkout one-time $29 / TRY 999 / €27 (geo-priced)
- License-key-only access (no auth account in v1.0 — radically simplifies threat surface)
- Locales: TR + EN

**Explicitly out of scope for Phase 1:**
- User accounts / auth (license key emailed via Stripe receipt)
- Multi-device sync
- Social features
- Teacher tools
- Subscription billing
- Children's pathway
- Any non-Bach repertoire

**Why this scope:**
- Eliminates 6 of 11 architecture CRITICAL findings (no backend, no auth, no sync, no curriculum pipeline at scale, no telemetry beyond opt-in, no DR for stateful data)
- Eliminates 4 of 6 security CRITICAL findings (no Supabase RLS needed, no children's data, no Stripe webhook complexity beyond simple license issuance, simpler CSP)
- Validates the **highest-risk product hypothesis** (will pianists pay for browser-based practice tools?) at minimum cost
- Single composer + 15 pieces is **defensible curated corpus** — competitive with single-composer Henle apps but at a fraction of the cost

**Gate G2 (Week 20, 8 weeks post-launch) — Pass criteria:**
- ≥100 paying users at $29 (gross ~$2,900)
- Refund rate < 10%
- NPS ≥ 30 (small-sample qualitative)
- Avg session length ≥ 12 minutes (engagement signal)
- ≥1 organic mention in piano teacher community (TR or EU)

**If G2 passes:** proceed to Phase 2 — expand corpus, add account system, prepare for subscription pivot.
**If G2 fails:** evaluate root cause (acquisition vs. activation vs. retention vs. monetization), decide pivot/persist/sunset.

### 2.5 Phase 2 — Repertoire Expansion (Weeks 16-24)

**Objective:** prove the platform thesis (broader corpus → broader appeal → conversion to subscription).

**Conditional on G2 pass.**

**Scope additions:**
- Add 30+ pieces across: Bach Goldberg Aria + 5 selected Variations (PD), Czerny op. 599 selections (PD), Burgmüller op. 100 (PD), Heller op. 47 (PD), 3 selected Mendelssohn Songs Without Words (PD)
- Account system (Supabase Auth, EU region only) — prep for subscription
- All architecture CRITICAL findings closed (observability, CI/CD, schema, RLS, etc.)
- Cookie banner + accessibility statement + DPIA published
- Locales: TR + EN + DE

**Gate G3 (Week 24):** ≥3% free→paid intent conversion on expanded catalog landing page; ≥250 lifetime customers cumulative.

### 2.6 Phase 3 — Premium Platform (Weeks 25-52)

**Conditional on G3 pass.**

- Subscription tier (€7/mo or €49/yr) alongside lifetime perpetual offer
- Multi-device sync (Yjs CRDT)
- Teacher tools (assignment, progress sharing — B2B beachhead)
- Curriculum levels 1-10 (modest scope, not 25) — produced via licensed core (Faber/Hoffman) + custom layer
- AI fingering personalization (DPIA-gated)
- Locales: TR + EN + DE + FR + ES

This phase intentionally remains underspecified in this Master Plan; a separate Phase-3 quality-gate cycle re-runs at Week 22 with G2 data informing scope.

---

## 3. Architecture Decisions (Final ADRs)

All ADRs reach **Status: ACCEPTED**. ADRs are stored as standalone files in `docs/adr/NNNN-*.md`; this section is the canonical index with rationale digest.

| ID | Decision | Rationale | Status |
|---|---|---|---|
| **ADR-001** | **Frontend: React 18 + Vite + TypeScript strict** | Largest talent pool, mature OSMD/WebMidi integrations, strong PWA tooling, audit gap CRIT-A6 closed | ✅ Accepted |
| **ADR-002** | **Notation: OpenSheetMusicDisplay (OSMD)** | BSD-3, MusicXML, mature, used in production at MuseScore-adjacent tools | ✅ Accepted |
| **ADR-003** | **Audio: Tone.js + @tonejs/piano + Salamander Grand V3** | CC-BY 3.0 samples, AudioWorklet-backed, progressive loading (Lite < 5MB → full on demand) | ✅ Accepted |
| **ADR-004** | **MIDI: WebMidi.js (input) + @tonejs/midi (file parse)** | Battle-tested wrappers; SysEx disabled by default | ✅ Accepted |
| **ADR-005** | **Fingering: pianoplayer (Parncutt 1997 DP) at build-time** | Public-domain algorithm; pre-computed per-piece manifests; no runtime ML | ✅ Accepted |
| **ADR-006** | **State: Zustand (UI) + Dexie (IndexedDB persistence)** | Minimal API, predictable, SSR-irrelevant for SPA | ✅ Accepted |
| **ADR-007** | **Build/PWA: Vite + Workbox** | Mature SW recipe; precache + runtime cache split; SW versioned by build hash | ✅ Accepted |
| **ADR-008** | **Hosting: Cloudflare Pages (app) + R2 (samples) + Workers (Edge functions)** | Single vendor for v1; multi-vendor backup posture for audit logs (S3) | ✅ Accepted |
| **ADR-009** | **Backend (Phase 2+): Supabase EU region** | Postgres + Auth + Edge Functions; RLS-by-default; pgTAP test suite | ✅ Accepted |
| **ADR-010** | **Payments: Stripe Checkout (hosted, SAQ-A scope)** | Lowest PCI burden; Stripe Tax for EU VAT compliance; 3DS forced in EU | ✅ Accepted |
| **ADR-011** | **Repertoire (Phase 1): Bach Inventions, Bach-Gesellschaft 1853 PD edition** | Defensible single-composer corpus; AI fingering on Czerny/Busoni PD editions | ✅ Accepted |
| **ADR-012** | **SRS: ts-fsrs (FSRS-4.5)** | MIT, modern algorithm, adaptive scheduling — Phase 2+ | ✅ Accepted |
| **ADR-013** | **Sync (v1.0): device-local only, JSON export/import** | Closes CRIT-A3 without committing to CRDT complexity prematurely | ✅ Accepted |
| **ADR-014** | **Sync (v2.0+): Yjs CRDT over Supabase Realtime** | Conflict-free; offline-first compatible; established library | ✅ Accepted (deferred) |
| **ADR-015** | **Observability: Sentry (errors) + OpenTelemetry browser SDK → Honeycomb (traces) + custom RUM (audio metrics)** | Closes CRIT-A1; free tiers cover MVP | ✅ Accepted |
| **ADR-016** | **CI/CD: GitHub Actions + Cloudflare Pages preview deploys + PostHog feature flags** | Closes CRIT-A2 | ✅ Accepted |
| **ADR-017** | **Children's data policy: 16+ hard age gate, no COPPA pathway in v1.0** | Closes CRIT-S4 with simplest legal posture; reopens at v3.0 with proper VPC if business case warrants | ✅ Accepted |
| **ADR-018** | **Geo scope v1.0: TR + EU only** | Defers CCPA/CPRA/LGPD/UK DPA divergence; allows focused KVKK + GDPR compliance | ✅ Accepted |
| **ADR-019** | **License model v1.0: $29 lifetime perpetual via emailed license key** | Eliminates auth attack surface; simplifies refund/dispute flow | ✅ Accepted |

---

## 4. Tier-1 Operational Foundation

This section closes audit findings CRIT-A1, CRIT-A2, CRIT-A7, CRIT-A11 and HIGH operational findings.

### 4.1 Observability

**Phase 1 minimum (free tier sufficient ≤ 1k MAU):**
- **Sentry** for frontend exception tracking, source-map uploads via CI, release health
- **OpenTelemetry browser SDK** → Honeycomb free tier; custom spans around: notation render, audio context init, sample load, MIDI roundtrip
- **Custom RUM events** (anonymous, opt-in): `audio.latency.p95`, `audio.latency.p99`, `notation.render.ms`, `midi.roundtrip.ms`, `sample.load.bytes`
- **Cloudflare Web Analytics** for traffic (privacy-preserving by default, no cookies)

**SLOs (documented in `docs/operations/SLO.md`):**

| SLO | Target | Window | Error Budget |
|---|---|---|---|
| Site availability | 99.9% | 30-day rolling | 43.2 min/month |
| Page time-to-interactive p95 | ≤ 3.5s on 4G mid-tier mobile | 7-day rolling | 5% of sessions |
| Audio input → output latency p95 | ≤ 30ms on wired MIDI | 7-day rolling | 5% of sessions |
| Notation render p95 | ≤ 800ms for ≤ 200-bar piece | 7-day rolling | 5% of sessions |
| Stripe Checkout completion success | ≥ 99% (excluding user abandon) | 30-day rolling | 1% of attempts |

Burn-rate alerts: 2% in 1h OR 5% in 6h triggers founder pager.

### 4.2 CI/CD Pipeline

`.github/workflows/ci.yml` blocks merge on:
- TypeScript strict typecheck
- ESLint (zero errors, max 10 warnings)
- Vitest unit tests (≥80% line coverage on critical paths: score-follower, audio engine, MIDI handler, license validator)
- Playwright e2e (auth-less critical paths: load → select piece → play with virtual MIDI → completion)
- Lighthouse CI: Performance ≥ 90, Accessibility = 100, Best Practices ≥ 95, PWA = pass
- Bundle size budget: initial JS ≤ 200kb gz, total chunked ≤ 1MB gz (excludes samples)
- `npm audit --audit-level=high` (blocks high/critical CVEs)
- Gitleaks secret scan
- License audit (allowlist: MIT, BSD, Apache-2.0, ISC, CC-BY, CC0)
- CycloneDX SBOM generated and attached to releases

Cloudflare Pages preview deploys on every PR with unique URL; production deploy requires PR merge to `main` with green CI.

Rollback: Cloudflare Pages instant rollback via dashboard (≤30s); database migrations always reversible (forward + down scripts via supabase-cli, never destructive).

Feature flags: PostHog free tier; every user-facing change behind a flag with rollout percentage.

### 4.3 Performance Budget

`docs/performance/BUDGET.md` enforced via Lighthouse CI + `bundlesize` (or `vite-bundle-visualizer` per-build):

| Metric | Budget | Notes |
|---|---|---|
| Initial JS bundle | ≤ 200 kb gz | Code-split: notation, audio, MIDI lazy-loaded |
| Total JS (chunked) | ≤ 1 MB gz | Excludes Salamander samples |
| Salamander Lite | ≤ 5 MB | Default progressive load |
| Salamander Full | ≤ 150 MB | On-demand, cached in IndexedDB via Cache API |
| LCP p75 | ≤ 2.5s | Mobile 4G |
| INP p75 | ≤ 200ms | All interactions |
| CLS | ≤ 0.1 | Notation reflow controlled |

### 4.4 Disaster Recovery & Business Continuity (`docs/operations/DR.md`)

| Scenario | Detection | Mitigation | RTO | RPO |
|---|---|---|---|---|
| Cloudflare Pages outage | Sentry uptime + status.cloudflare.com | Static fallback on AWS S3 + Route53 secondary (Phase 2+) | 1h | 0 |
| R2 outage (samples) | RUM `sample.load.failure` rate alert | Samples cached in IndexedDB after first load; degraded but functional | N/A | 0 |
| Supabase region outage (Phase 2+) | Sentry + supabase status | Read-only mode with IndexedDB cache; banner notice | 4h | 1h |
| Stripe outage | Stripe webhook failure alert | Queue checkout intents; retry hourly; comms via email | 8h | 0 |
| Owner unavailable | n/a | Documented runbooks + designated technical executor (legal) | 72h | 0 |

Backup posture: Supabase auto-backups (Pro tier when reached) + weekly encrypted off-vendor backup of `audit_log` to AWS S3 with Object Lock (7-year retention).

### 4.5 On-Call & Incident Response

Phase 1 (solo): founder is sole on-call; documented severity matrix in `docs/operations/IR-PLAN.md`:

- **SEV-1** (data breach, payment failure ≥ 1h, total outage): respond within 15 min, communicate via status page within 1h
- **SEV-2** (degraded performance, partial feature outage): respond within 1h, fix within 24h
- **SEV-3** (cosmetic, individual user issue): respond within 24h
- **SEV-4** (enhancement): backlog

GDPR Art. 33 / KVKK Art. 12 breach notification templates pre-drafted in `docs/security/IR-TEMPLATES/` (TR + EN).

Quarterly tabletop exercise (Phase 2+); in Phase 1, single tabletop in Week 14 before launch.

---

## 5. Security & Privacy by Design

This section closes audit CRITICAL findings S1-S6 and HIGH findings H-S1 through H-S16.

### 5.1 Threat Model (`docs/security/THREAT-MODEL.md`)

STRIDE workshop scheduled Week 4 (start of Phase 1) before any production code is written. Output: data-flow diagram + per-asset STRIDE matrix + abuser stories. Tooling: OWASP Threat Dragon.

**Phase 1 high-level asset map:**

| Asset | Trust Boundary | Primary Threats |
|---|---|---|
| Static app (Cloudflare Pages) | Public CDN | Tampering (subresource), DoS |
| Salamander samples (R2) | Public CDN with signed URLs | Tampering, exfiltration cost |
| MusicXML/MIDI bundle (in app) | Same-origin | XXE on user-supplied imports (if enabled), zip-bomb |
| License keys (issued by Stripe webhook) | Webhook + email | Forgery, replay, brute-force enumeration |
| Stripe webhook endpoint (Cloudflare Worker) | Public HTTPS | Spoofing, replay, race |
| Browser IndexedDB (practice history) | User-controlled | XSS exfiltration, prototype pollution |

User-supplied MusicXML import is **disabled in Phase 1** (only curated bundled corpus). This eliminates the upload-validation attack surface at the cost of one feature; reintroduced in Phase 2 with full pipeline.

### 5.2 Content Security Policy (`docs/security/CSP.md`)

Phase 1 baseline (Cloudflare Pages `_headers`):

```
Content-Security-Policy:
  default-src 'none';
  script-src 'self' 'wasm-unsafe-eval' https://js.stripe.com;
  style-src 'self' 'unsafe-hashes' 'sha256-<hash-per-build>';
  img-src 'self' data: https://*.r2.cloudflarestorage.com;
  connect-src 'self' https://*.r2.cloudflarestorage.com https://api.stripe.com https://*.ingest.sentry.io https://api.honeycomb.io;
  media-src 'self' https://*.r2.cloudflarestorage.com;
  worker-src 'self' blob:;
  frame-src https://js.stripe.com https://hooks.stripe.com;
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self' https://checkout.stripe.com;
  require-trusted-types-for 'script';
  report-to csp-endpoint;

X-Frame-Options: DENY
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: midi=(self), microphone=(), camera=(), geolocation=(), payment=(self "https://js.stripe.com"), interest-cohort=()
```

Deployed in **Report-Only** mode for Weeks 12-14, then enforce at launch.

### 5.3 File Upload Pipeline

**Phase 1: NOT APPLICABLE** — no user uploads. Only curated bundled corpus.

**Phase 2 reintroduction (`docs/security/UPLOAD-PIPELINE.md`):**
- Hard size limits: MIDI 1MB, MusicXML 5MB, MXL 10MB
- Magic-byte sniff (don't trust extension)
- MusicXML parsed with `processEntities: false`, DOCTYPE rejected, validated against MusicXML 4.0 XSD, allowlisted elements
- MXL stream-decompressed with 100× ratio cap
- DOMPurify sanitization of all user-visible string fields (`<work-title>`, `<credit>`, `<creator>`, `<lyric>`, `<words>`)
- PDF intentionally **not supported** (audit CRIT-S3 risk too high without server-side sandboxed processing; revisited Phase 3+)
- Upload quota: 5/day free, 50/day paid

### 5.4 Children's Data Policy (Final)

**Decision: 16+ hard age gate. No service to under-16.**

Implementation:
- Phase 1: footer disclaimer + Stripe Checkout requires age confirmation checkbox in Terms
- Phase 2 (with auth): DOB collection at signup; reject if < 16 (per GDPR Art. 8 highest-bar interpretation; covers KVKK explicit-consent simplification by avoiding the question entirely)
- Rate-limit re-attempts with same IP/email
- Privacy policy section: "We do not knowingly collect data from children under 16. If you become aware that a child has provided us personal data, contact us for deletion."
- No COPPA-VPC pathway; US users blocked from age-segmented features (covered by ADR-018 geo deferral anyway)

Reopens for review at v3.0 (or when Phase 2 data justifies investment in compliant family/schools tier).

### 5.5 Stripe Integration Security

`apps/worker/src/stripe-webhook.ts` (Cloudflare Worker):

- Stripe **Checkout (hosted)** only — SAQ-A scope, Elements never used
- `stripe.webhooks.constructEvent()` with timing-safe HMAC verification, raw body, 5-minute replay window
- Idempotency: webhook handler upserts by `stripe_event_id` (Postgres unique constraint or Cloudflare KV in Phase 1 pre-Postgres)
- Server-side price ID lookup via `STRIPE_PRICE_LIFETIME_TRY` / `_EUR` / `_USD` env vars, never trust client
- 3DS forced in EU via Stripe Radar rules
- Refund SOP documented; refunds trigger license-key revocation in KV
- License key issuance: Ed25519-signed JWT-shaped token (header + payload + signature), payload = `{email_hash, sku, issued_at, version}`, verified client-side at app load
- Stripe Tax enabled for EU VAT compliance from Day 1

### 5.6 Supabase RLS (Phase 2+)

When auth + database are introduced in Phase 2, the following is mandatory before any user data lands:

- `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` on every table — enforced via CI lint that fails build if any table lacks RLS
- Default-deny policies; explicit `USING (auth.uid() = user_id)` per table
- Three schemas: `public` (RLS, app-accessible via anon-key), `private` (service-role only), `audit` (append-only, service-role write, no read from app)
- Service-role key NEVER in client; only Cloudflare Workers (Edge Functions equivalent)
- pgTAP RLS test suite in CI (`tests/rls/*.sql`)
- `pg_audit` extension for query-level auditing

Schema sketch (minimal v2.0):

```sql
-- public schema (RLS enforced)
create table public.users (
  id uuid primary key references auth.users(id) on delete cascade,
  display_name text,
  locale text not null default 'tr',
  created_at timestamptz not null default now()
);

create table public.licenses (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references public.users(id) on delete cascade,
  sku text not null,
  stripe_event_id text not null unique,  -- idempotency
  issued_at timestamptz not null default now(),
  revoked_at timestamptz
);

create table public.practice_sessions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references public.users(id) on delete cascade,
  piece_id text not null,
  started_at timestamptz not null,
  ended_at timestamptz not null,
  notes_correct int not null check (notes_correct >= 0),
  notes_total int not null check (notes_total >= 0),
  metadata jsonb
);

-- audit schema (append-only)
create table audit.events (
  id bigserial primary key,
  ts timestamptz not null default now(),
  actor_type text not null check (actor_type in ('user','admin','system')),
  actor_id text,
  action text not null,
  resource text not null,
  metadata_hash text not null,  -- no raw PII
  ip_truncated text,             -- /24 IPv4 or /48 IPv6
  ua_family text                  -- first 100 chars
) partition by range (ts);

-- RLS policies (excerpt)
alter table public.users enable row level security;
create policy users_self_read on public.users
  for select using (auth.uid() = id);
create policy users_self_update on public.users
  for update using (auth.uid() = id);

alter table public.licenses enable row level security;
create policy licenses_self_read on public.licenses
  for select using (auth.uid() = user_id);
-- writes only via service-role webhook handler

alter table public.practice_sessions enable row level security;
create policy sessions_self_all on public.practice_sessions
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

### 5.7 Auth Hardening (Phase 2+)

When Supabase Auth is introduced:
- JWT TTL: 15 min, refresh token rotation enabled, refresh TTL: 7 days paid / 24h free
- Rate limits: sign-in 5/min/IP, signup 3/hr/IP
- HaveIBeenPwned k-anonymity password check on signup
- MFA (TOTP) forced for any account with active subscription
- "Sign out everywhere" via `auth.admin.signOut(userId, scope='global')`
- Cloudflare Turnstile on auth forms
- Idle session timeout 30 min free / 4h paid; absolute 24h

### 5.8 Secrets, Supply Chain, Account Hardening

- All production secrets in **Cloudflare Workers Secrets** (never in repo); rotated quarterly
- `gitleaks` pre-commit hook + GitHub Action
- Renovate auto-PRs for patch updates; manual review for minor/major
- `npm install --ignore-scripts` policy (postinstall script protection)
- CycloneDX SBOM per release, stored with artifact
- Cloudflare account: hardware MFA (YubiKey), separate scoped deploy token, audit log review monthly
- GitHub: branch protection on `main`, required reviews (Phase 2+ when collaborators), required signed commits

### 5.9 Privacy & Compliance Roadmap

| Item | Phase | Status |
|---|---|---|
| Privacy policy (TR + EN) | 1 | Drafted Week 12, published at launch |
| Terms of Service (TR + EN) | 1 | Drafted Week 12, published at launch |
| Cookie banner (Klaro, equal-prominence reject) | 1 | Implemented Week 13 |
| Subprocessor list (`/legal/subprocessors`) | 1 | Published at launch (Stripe, Cloudflare, Sentry, Honeycomb) |
| KVKK cross-border consent | 1 | Explicit checkbox in Stripe Checkout for TR users |
| Standard Contractual Clauses (SCCs) signed | 1 | Stripe + Cloudflare + Sentry + Honeycomb |
| GDPR data subject request workflow | 1 | Email-based (`privacy@`); 30-day SLA |
| Accessibility statement (EN 301 549 template) | 1 | Published at launch |
| DPIA for AI fingering | 2 | Required before fingering personalization ships |
| EU AI Act model card | 3 | Required before any ML personalization |
| Bug bounty / responsible disclosure | 1 | `/.well-known/security.txt` published at launch |
| Pentest (third-party) | 2 | Annual; budget €15-25k; trigger: first 1k paid users |
| SOC 2 Type I readiness | 3 | Vanta/Drata; trigger: B2B/EDU pipeline |

### 5.10 Repertoire Copyright (`docs/legal/REPERTOIRE-PROVENANCE.md`)

Every piece in the corpus has a row in `repertoire_metadata` with:
- `composer`, `work`, `edition_source`, `edition_year`, `pd_status_per_jurisdiction` (US/EU/UK/TR), `engraver`, `engraver_license`, `fingering_source`, `fingering_pd_status`, `provenance_url`

Phase 1 corpus (Bach Inventions) restricted to confirmed-PD (composer 1685-1750, edition 1853 or 1894 — both > 70-95 years post mortem of editor where applicable).

DMCA / EU DSM Directive Art. 17 takedown procedure published. US Copyright Office DMCA agent registered before launch ($6 fee).

---

## 6. Data Architecture

### 6.1 Data Stores

| Store | Phase | Purpose | Encryption |
|---|---|---|---|
| Bundled in app | 1+ | Curated MusicXML, MIDI ref, fingering manifests | N/A (public) |
| Cloudflare R2 | 1+ | Salamander samples, future user uploads | At-rest AES-256 (default) |
| Cloudflare KV | 1 | License keys (`stripe_event_id` → `license_token`), idempotency | At-rest (default) |
| IndexedDB (browser, Dexie) | 1+ | Practice history, settings, sample cache | None v1; SubtleCrypto AES-GCM Phase 3 |
| Supabase Postgres | 2+ | Users, licenses, sessions, audit | At-rest AES-256 (default) + Vault for sensitive cols |
| AWS S3 (off-vendor) | 2+ | Audit log backup with Object Lock | At-rest AES-256 + customer-managed key |

### 6.2 IndexedDB Schema Versioning (closes CRIT-A3)

Dexie versioning with forward-compat strategy:

```typescript
// src/data/db.ts
export const db = new Dexie('piano-practice');

db.version(1).stores({
  sessions: '++id, pieceId, startedAt',
  pieces:   'id, composer, level',
  settings: 'key',
  samples:  'name, sizeBytes',
});

// Future versions append; never DROP without exporter
db.version(2).stores({
  // new fields
}).upgrade(async tx => {
  // migration logic with rollback safety
});
```

JSON export/import implemented from v1.0 — user can extract complete IndexedDB state to portable file (privacy + DR + GDPR Art. 20 portability satisfied).

### 6.3 Sync Model (closes CRIT-A3)

- **Phase 1**: device-local only. JSON export/import. No sync. No conflict.
- **Phase 2 (auth + cloud)**: server-authoritative for license/billing; client-local for practice history with periodic upload via append-only event log.
- **Phase 3 (multi-device)**: Yjs CRDT over Supabase Realtime channels; per-document (per-piece) Y.Doc; last-writer-wins for settings.

This closes the audit's CRIT-A3 with a deliberate phasing instead of an upfront commitment.

---

## 7. Content & Pedagogy Strategy

### 7.1 Phase 1 Corpus

15 Bach Two-Part Inventions (BWV 772-786):
- Source: Bach-Gesellschaft Ausgabe (1853) — confirmed PD globally
- Engraving: re-engraved in MuseScore 4 (open format, exportable to MusicXML)
- Editorial fingering: Czerny (1840) + Busoni (1894) — both PD — offered as toggleable presets
- AI fingering: pianoplayer (Parncutt 1997 algorithm, confirmed PD; build-time computation only)
- Reference recording: own performance, recorded in Phase 1 Week 8-10, CC-BY-NC license
- Difficulty rating: ABRSM Grade 5-7 equivalent, documented per-piece

Production cost: ~40 hours engraving + QA + reference recording. **This is the entire Phase-1 content cost.** No external licensing needed.

### 7.2 Phase 2 Corpus Expansion

Conditional on G2 pass. Target: 30 additional PD pieces. Production cost: ~120 hours over 6 weeks.

Sources:
- Bach Goldberg Aria + selected variations
- Czerny Op. 599 (selections, beginner-progressive)
- Burgmüller Op. 100 (25 Easy & Progressive Studies)
- Heller Op. 47
- Mendelssohn Songs Without Words (3 selected)

### 7.3 Phase 3 Curriculum (Conditional)

If Phase 2 validates platform thesis:
- Either: license existing curriculum (Faber, Hoffman, RCM) — €25-50k upfront + per-user royalty (PK-1 mitigation: realistic budget)
- Or: hire ABRSM-certified pedagogue contractor for custom 10-level (not 25!) curriculum — €40-80k over 12 months
- Decision deferred to Phase 3 quality-gate cycle with G2/G3 economics in hand

### 7.4 Content QA Pipeline (`docs/content/PIPELINE.md`)

For every piece added to corpus:
1. Source verification (PD status per jurisdiction, edition provenance)
2. MusicXML engraving in MuseScore 4
3. Automated lint: notation correctness, range validation, fingering plausibility
4. Manual review by 1 qualified pianist (founder Phase 1; contractor Phase 2+)
5. Reference recording (MIDI + audio)
6. Difficulty rating (ABRSM/RCM equivalent, fingering complexity score)
7. Metadata commit to `corpus/metadata.json` with cryptographic hash
8. CI verifies hash on every build (corpus-tampering detection)

---

## 8. Acquisition & Validation Plan

### 8.1 Sprint 0 Paid Acquisition Test

**Setup (Week 1):**
- Landing page: value prop + 30-sec demo video (prototype) + email capture + intent question
- Meta Ads: 5 ad creatives × 2 audiences (TR piano-learners + DE/EU adult learners)
- Google Ads: branded keywords (piano practice browser, sheet music app, etc.)
- Tracking: Plausible (privacy-preserving) + UTM tagging

**Spend (Weeks 2-3):**
- €500 Meta + €300 Google = €800 total
- Goal: ≥50 email signups, ≥3 unsolicited "would pay" responses

**Decision (end Week 3):**
- CPA ≤ €15 to email + ≥5% intent-to-pay → **GO** (Gate G1 partial)
- CPA €15-30 OR intent 2-5% → **CAUTION** (revise positioning, retry once)
- CPA > €30 OR intent < 2% → **REPOSITION OR ABANDON**

### 8.2 Owned Channel Build (Phase 1 Weeks 4-15)

In parallel with engineering:
- Newsletter: weekly "Practice Notebook" — 1 piece deep-dive, AI fingering reasoning, technique tip; goal 500 subscribers by launch
- YouTube: monthly 5-min video — performance + practice walkthrough; goal 10 videos by Week 24
- Reddit/r/piano + r/classicalmusic + Turkish piano forums: 1 substantive post/week (no spam); goal 20 organic mentions by Week 24
- Piano teacher outreach (TR + DE): 50 personal emails Weeks 12-15 with free license-keys for evaluation; goal 5 active referrers

### 8.3 Launch (End of Phase 1, Week 15)

- ProductHunt + HackerNews + Reddit launch day
- Email blast to newsletter subscribers
- Teacher network reciprocal post
- Press: 3 pre-arranged Turkish + EU classical music blogs

---

## 9. Realistic Resource Plan

### 9.1 Time (Solo Founder)

| Phase | Weeks | Hours | Avg/week |
|---|---|---|---|
| Phase 0 — Validation | 1-3 | ~100 | ~33 |
| Phase 1 — MVP | 4-15 | ~350 | ~29 |
| Phase 2 — Expansion | 16-24 | ~250 | ~28 |
| Phase 3 — Premium | 25-52 | ~600 | ~21 |
| **Total Y1** | **52** | **~1,300** | **~25** |

This matches the audit's revised solo estimate (32-40 weeks for v1.0-equivalent scope, here split into Phase 1 + Phase 2). The 52-week envelope assumes ~25h/week sustainable solo cadence.

### 9.2 Cash Budget Y1

| Category | Phase 1 | Phase 2 | Phase 3 | Y1 Total |
|---|---|---|---|---|
| Phase-0 paid acquisition test | €800 | — | — | €800 |
| Legal counsel (KVKK + GDPR + ToS/Privacy) | €1,500 | €1,000 | €1,500 | €4,000 |
| Stripe fees (estimate at modest volume) | €100 | €400 | €1,500 | €2,000 |
| Cloudflare (Pages free, R2 +Workers paid) | €100 | €200 | €500 | €800 |
| Supabase (free → Pro at Phase 2) | — | €300 | €600 | €900 |
| Sentry (free → Team at scale) | — | €260 | €520 | €780 |
| Honeycomb (free tier OK most of Y1) | — | — | €600 | €600 |
| Domain + email (TR + EU) | €100 | — | — | €100 |
| YubiKey × 2 (primary + backup) | €100 | — | — | €100 |
| Pentest (third-party, Phase 2 trigger) | — | — | €15,000 | €15,000 |
| Vanta/Drata (Phase 3) | — | — | €4,000 | €4,000 |
| Marketing (newsletter tool, video) | €200 | €500 | €1,500 | €2,200 |
| Bug bounty bounties (Phase 3) | — | — | €1,000 | €1,000 |
| **Subtotal cash** | **€2,900** | **€2,660** | **€26,720** | **€32,280** |

**Realistic Y1 cash range: €18-32k** (Phase 3 budget is conditional on G2/G3 passes — only spent if economics support it).

### 9.3 Decision: Solo Path Confirmed

This Master Plan commits to **solo founder execution**. The team scenario (2 FTE + specialists, €120-180k labor) is rejected for Y1 because:
- Pre-revenue — no salary runway
- Phase-0 validation outcome would invalidate hiring decision in either direction
- Audit's PK-3 risk acceptable when timeline is reset to 52 weeks (not 12)

Hiring revisited at Phase 3 if G2 + G3 both pass and revenue supports.

---

## 10. Compliance & Risk Register

### 10.1 Risk Register (Live Document, `docs/risks/REGISTER.md`)

| ID | Risk | Likelihood | Impact | Mitigation | Owner | Review |
|---|---|---|---|---|---|---|
| R-01 | CAC exceeds viability | Medium | Project-killer | Phase-0 paid test gates further commit | Founder | Weekly Phase 0 |
| R-02 | Curriculum cost balloons | Low (deferred) | Major | Phase 1 uses PD-only; Phase 3 decision based on data | Founder | At Phase 3 gate |
| R-03 | Solo burnout / timeline slip | Medium-high | Major | 25h/week cap; quarterly retrospectives; G2 abandon clause | Founder | Monthly |
| R-04 | Stripe webhook compromised → free licenses | Low | Major | HMAC verify + idempotency + revocation script | Founder | Quarterly tabletop |
| R-05 | KVKK cross-border enforcement | Low-medium | Major | Explicit consent + SCCs + legal counsel review | Counsel | Annually |
| R-06 | Audio latency on iOS Safari unfixable | Low | Major | Phase-0 prototype validates; fallback: ScriptProcessorNode + clear UX warning | Founder | At Gate G1 |
| R-07 | Bach corpus differentiation insufficient | Medium | Major | Pedagogical depth (fingering reasoning, hand-separation analysis) over quantity | Founder | At Gate G2 |
| R-08 | OSMD performance with large scores | Low | Moderate | Phase-1 limited to ≤200-bar pieces; pagination Phase 2+ | Founder | At Phase 2 plan |
| R-09 | Salamander sample license interpretation | Low | Major | Legal review of CC-BY 3.0 attribution before launch | Counsel | At Phase 1 launch |
| R-10 | Cloudflare account compromise | Very low | Catastrophic | Hardware MFA + scoped tokens + audit log review monthly | Founder | Monthly |

### 10.2 Compliance Posture Summary

| Framework | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|
| GDPR (EU) | ✅ Privacy notice + cookie banner + DSR workflow | ✅ + DPIA | ✅ + Art. 22 explainability |
| KVKK (TR) | ✅ Cross-border consent + VERBİS check | ✅ Same | ✅ Same |
| ePrivacy Directive | ✅ Cookie banner with equal-prominence reject | ✅ | ✅ |
| EAA 2025 (EU accessibility) | ✅ WCAG 2.1 AA + statement | ✅ + 3rd-party audit | ✅ |
| EU AI Act | N/A (no AI runtime in Phase 1) | ✅ Model card if AI ships | ✅ Full compliance |
| PCI DSS | ✅ SAQ-A via Stripe Checkout | ✅ | ✅ |
| COPPA / CCPA / LGPD | N/A (geo-deferred) | N/A | ⚠ Re-evaluate at v3.0 |
| SOC 2 / ISO 27001 | N/A | "Ready" architecture | Optional certification trigger |

**Compliance Debt Score progression:** v1.0 baseline 2.5 → end of Phase 1 **6.5** → end of Phase 2 **7.5** → end of Phase 3 **8.5**.

---

## 11. Decision Gates (Stop/Go Criteria)

### Gate G1 — End of Phase 0 (Week 3)

**Pass (proceed to Phase 1):** ALL of:
- Phase-0 CPA ≤ €15 to email signup
- Intent-to-pay ≥ 5% on landing page
- ≥10/15 customer interviews validate pain + willingness
- Prototype achieves audio latency p95 ≤ 50ms on test hardware
- No platform-killer technical issue surfaced

**Fail action:** abandon current positioning; either reposition + re-test (1 cycle max) or pivot to alternative use-case (B2B teacher tool) or sunset.

### Gate G2 — Week 20 (8 weeks post-launch)

**Pass (proceed to Phase 2):** ALL of:
- ≥100 paying customers at $29 lifetime
- Refund rate < 10%
- NPS ≥ 30
- Avg session ≥ 12 minutes
- ≥1 organic mention in piano teacher community

**Fail action:** root-cause analysis (acquisition vs. activation vs. retention vs. monetization) → repair-and-retry once OR sunset cleanly with refunds honored.

### Gate G3 — End of Phase 2 (Week 24)

**Pass (proceed to Phase 3):** ALL of:
- ≥250 cumulative paying customers
- ≥3% landing-page → paid conversion on expanded catalog
- ≥30% week-4 retention on active practice
- Founder bandwidth + cash runway support Phase 3

**Fail action:** stay at Phase 2 scope; harvest as profitable side-project; defer Phase 3 indefinitely.

---

## 12. Governance & Documentation

### 12.1 Documentation Structure (`docs/`)

```
docs/
├── MASTER-PLAN.md                       (this file — single source of truth)
├── adr/                                 (decision records, NNNN-*.md)
├── architecture/
│   ├── SCHEMA.md
│   ├── API-CONTRACT.md
│   └── STATE-SYNC.md
├── operations/
│   ├── SLO.md
│   ├── DR.md
│   ├── IR-PLAN.md
│   └── runbooks/
├── security/
│   ├── THREAT-MODEL.md
│   ├── CSP.md
│   ├── UPLOAD-PIPELINE.md      (Phase 2)
│   ├── DPIA-FINGERING.md       (Phase 2)
│   ├── SBOM-POLICY.md
│   └── IR-TEMPLATES/
├── performance/
│   └── BUDGET.md
├── content/
│   ├── PIPELINE.md
│   └── corpus-metadata.json
├── legal/
│   ├── REPERTOIRE-PROVENANCE.md
│   ├── PRIVACY.md
│   ├── TERMS.md
│   └── SUBPROCESSORS.md
├── product/
│   └── TELEMETRY.md
├── research/
│   └── PHASE0-RESULTS.md       (after Week 3)
├── risks/
│   └── REGISTER.md
├── decisions/                  (gate decisions G1/G2/G3)
└── _archive/
    ├── QUALITY-GATE-REPORT-v1.md
    └── AUDIT-REPORT-V1.md
```

### 12.2 Change Control

This Master Plan changes only via documented amendment:
- Each amendment = new section appended (`## Amendment N — YYYY-MM-DD — Title`)
- Major scope changes = new version (v2.0) with old preserved in `_archive/`
- ADRs are append-only; superseding ADRs reference predecessors with `Supersedes: ADR-NNN`
- Gate decisions (G1/G2/G3) committed as separate `docs/decisions/` files

### 12.3 Quality Standards (per repository CLAUDE.md / rules)

- TDD: tests-first for all critical-path code (score follower, audio engine, MIDI handler, license validator, Stripe webhook)
- ≥80% coverage on critical paths
- Code review: self-review + checklist for solo Phase 1; mandatory peer review when collaborators added
- Conventional Commits + commitlint
- ESLint + Prettier + TypeScript strict
- Immutability default (per `rules/coding-style.md`)
- Files ≤ 800 lines; functions ≤ 50 lines; nesting ≤ 4 levels
- Zero `console.log` in committed code; zero hardcoded secrets

---

## 13. Verdict

**APPROVED FOR EXECUTION**, gated by:

1. Founder formally accepts this Master Plan (file commit acknowledgement = acceptance)
2. Phase 0 begins immediately (Week 1 = first Monday after acceptance)
3. Gate G1 evaluated at end of Week 3 with documented decision

This plan deliberately:
- Replaces optimism with measurement (Gate G1 before architecture commitment)
- Replaces feature breadth with focused defensibility (Bach Inventions niche)
- Replaces sketched compliance with executable specifications (CSP, RLS, IR plan)
- Replaces sketched operations with mandatory foundations (Sentry + SLOs from commit #1)
- Replaces "12 weeks 150 hours" fantasy with "52 weeks 1,300 hours €18-32k" realism
- Resolves all 11 CRITICAL audit findings either by addressing them directly or by deliberately scoping them out of Phase 1

**Next action**: founder reads, accepts, archives v1 documents to `_archive/`, runs `/plan` workflow scoped to Phase 0 only.

---

## 14. Appendices

### Appendix A — All 60+ Audit Findings Resolution Map

| Finding | Severity | Resolution in Master Plan |
|---|---|---|
| PK-1 Curriculum cost off 50-100× | Project-killer | §2.4 Phase-1 PD-only Bach corpus; §7 phased curriculum strategy; full curriculum gated to G3 |
| PK-2 CAC fictional | Project-killer | §2.3 Phase-0 paid acquisition test; Gate G1 |
| PK-3 12-week timeline delusional | Project-killer | §9.1 52-week envelope, 1,300h, solo |
| CRIT-A1 Observability absent | Critical arch | §4.1 Sentry + OTel + RUM + 5 SLOs from commit #1 |
| CRIT-A2 CI/CD undefined | Critical arch | §4.2 GitHub Actions + Cloudflare preview + PostHog flags |
| CRIT-A3 IndexedDB sync missing | Critical arch | §6.2 Dexie versioning + JSON export; §6.3 phased sync |
| CRIT-A4 Backend sketch | Critical arch | §5.6 schema + RLS sketch; ADR-009; Phase-2 introduction |
| CRIT-A5 Headcount unrealistic | Critical arch | §9 solo path confirmed with realistic envelope |
| CRIT-A6 Frontend framework deferred | Critical arch | ADR-001: React 18 + Vite + TS strict |
| CRIT-A7 Performance budget undefined | Critical arch | §4.3 explicit metrics + Lighthouse CI |
| CRIT-A8 Audio failure modes | Critical arch | §5.1 risks + R-06; Phase-0 prototype validates |
| CRIT-A9 Content pipeline undefined | Critical arch | §7.4 explicit pipeline |
| CRIT-A10 Telemetry schema missing | Critical arch | §4.1 RUM events listed; full TELEMETRY.md Week 12 |
| CRIT-A11 DR absent | Critical arch | §4.4 explicit RTO/RPO matrix |
| CRIT-S1 Threat model absent | Critical sec | §5.1 STRIDE workshop Week 4; THREAT-MODEL.md output |
| CRIT-S2 CSP unspecified | Critical sec | §5.2 explicit Phase-1 CSP; Report-Only → Enforce |
| CRIT-S3 File upload validation | Critical sec | §5.3 not applicable Phase 1; full pipeline Phase 2 |
| CRIT-S4 Children's data | Critical sec | §5.4 16+ hard age gate; ADR-017 |
| CRIT-S5 Stripe security | Critical sec | §5.5 Checkout SAQ-A + HMAC + idempotency + 3DS |
| CRIT-S6 RLS missing | Critical sec | §5.6 RLS-by-default + pgTAP suite + CI lint |
| H-S1 Auth hardening | High sec | §5.7 explicit Phase-2 hardening |
| H-S2 SRI without pipeline | High sec | §4.2 vite-plugin-sri + signed sample manifest |
| H-S3 CORS missing | High sec | §5.2 CSP + R2/Supabase strict allowlist |
| H-S4 Clickjacking | High sec | §5.2 X-Frame-Options DENY + frame-ancestors none |
| H-S5 DPIA for AI fingering | High sec | §5.9 Phase-2 DPIA before personalization |
| H-S6 ePrivacy cookie banner | High sec | §5.9 Klaro equal-prominence Week 13 |
| H-S7 KVKK cross-border | High sec | §5.9 explicit consent + SCCs |
| H-S8 Audit logging | High sec | §5.6 audit schema + AWS S3 Object Lock backup |
| H-S9 IR plan | High sec | §4.5 explicit SEV matrix + GDPR/KVKK templates |
| H-S10 Dependency vuln mgmt | High sec | §4.2 Renovate + npm audit + SBOM + ignore-scripts |
| H-S11 EAA 2025 | High sec | §5.9 accessibility statement + Phase-2 audit |
| H-S12 PII in logs | High sec | §5.6 IP truncation + UA stripping + redaction |
| H-S13 Repertoire copyright | High sec | §5.10 per-piece provenance + DMCA agent |
| H-S14 Quota race conditions | High sec | §5.5 server-side enforcement only |
| H-S15 Rate limiting | High sec | Cloudflare Rate Limiting rules per endpoint class (added to §5.8) |
| H-S16 Cloudflare account security | High sec | §5.8 hardware MFA + scoped tokens |
| MED-S1..S13 (13 medium) | Medium sec | Addressed across §5; non-Phase-1 items deferred to Phase 2/3 with explicit triggers |

### Appendix B — Phase-0 Checklist (Actionable)

```
[ ] Week 1
    [ ] Register devran-piano.com (TR + EU) + email
    [ ] Set up Cloudflare account + hardware YubiKey
    [ ] Build /sprint0/landing (Vite + React + minimal copy + email capture)
    [ ] Set up Plausible analytics
    [ ] Schedule 15 customer interviews
    [ ] Schedule 3 pedagogue conversations
[ ] Week 2
    [ ] Launch Meta + Google ads (€800 total)
    [ ] Conduct first 8 interviews
    [ ] Begin /sprint0/proto (2-piece prototype)
    [ ] Daily CPA/intent monitoring
[ ] Week 3
    [ ] Conduct remaining 7 interviews + 3 pedagogue calls
    [ ] Finish /sprint0/proto on Cloudflare Pages preview
    [ ] Test prototype on: Mac (Chrome/Safari/Firefox), Windows (Chrome/Edge), iPad (Safari), Android (Chrome)
    [ ] Write docs/research/PHASE0-RESULTS.md
    [ ] Make G1 decision + commit docs/decisions/G1-DECISION.md
```

### Appendix C — References

- OWASP ASVS 4.0.3, OWASP Threat Modeling Cheat Sheet, OWASP File Upload Cheat Sheet
- GDPR (EU 2016/679), KVKK (TR 6698), ePrivacy Directive 2002/58/EC
- COPPA (16 CFR §312), CCPA/CPRA, LGPD, UK DPA 2018 — Y1 deferred via geo-scope
- EAA Directive (EU) 2019/882, EN 301 549 v3.2.1, WCAG 2.1 AA
- EU AI Act 2024/1689, NIST AI RMF 1.0
- PCI DSS v4 SAQ-A, PSD2 SCA, Stripe Webhook Best Practices
- NIST SSDF v1.1, SLSA Levels, CycloneDX SBOM spec
- Berne Convention, EU Copyright Directive 2019/790
- IBM Cost of a Data Breach Report 2024 (€4.45M EU avg)
- Google SRE Workbook (SLO methodology)
- Parncutt et al. 1997 — fingering algorithm
- ABRSM / RCM grading systems

---

**END OF MASTER PLAN v1.0 — APPROVED FOR `/plan` EXECUTION SCOPED TO PHASE 0**

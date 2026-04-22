# Performance Budget — Piano-Practice

> **Authority:** `MASTER-PLAN.md §4.3` (performance budget).
> **Enforcement:** Lighthouse CI + `vite-bundle-visualizer` + RUM in production.
> **Policy:** a PR that breaks budget cannot merge without explicit override documented in the PR description.

---

## 1. Budget Table

| Metric | Budget | Scope | Enforcement |
|---|---|---|---|
| Initial JS gz | ≤ 200 kb | App shell first-load | Lighthouse CI + bundle audit |
| Total JS gz (chunked) | ≤ 1 MB | All app code excluding samples | bundle audit |
| Salamander Lite | ≤ 5 MB | First sample set | sample-set audit script |
| Salamander Full | ≤ 150 MB | On-demand sample set, cached | acceptance on import |
| LCP p75 | ≤ 2.5 s | 4G mid-tier mobile | Lighthouse CI + RUM |
| INP p75 | ≤ 200 ms | All interactions | Lighthouse CI + RUM |
| CLS | ≤ 0.1 | All layouts | Lighthouse CI |
| Audio input → output latency p95 | ≤ 30 ms | Wired MIDI | RUM (`audio.latency.p95`) |
| Notation render p95 | ≤ 800 ms | ≤ 200-bar piece | RUM (`notation.render.ms`) |
| Stripe Checkout → license in hand p95 | ≤ 60 s | e2e | Integration test + webhook trace |

---

## 2. Route-Level Budgets

| Route | Initial chunk | TTI target (4G) |
|---|---|---|
| `/` | ≤ 80 kb gz | ≤ 2.5 s |
| `/unlock` | ≤ 40 kb gz (code-split) | ≤ 2.0 s |
| `/app` | ≤ 150 kb gz | ≤ 2.5 s |
| `/app/practice/:id` | ≤ 300 kb gz (lazy OSMD + Tone.js + WebMIDI) | ≤ 3.5 s first, ≤ 1.5 s cached |
| `/app/settings` | lazy-loaded chunk ≤ 60 kb gz | ≤ 2.0 s |
| `/legal/*` | pre-rendered HTML; no SPA JS required | ≤ 1.5 s |

---

## 3. Strategies in Use

- **Route-level code splitting.** Practice, Settings, Legal are separate chunks.
- **Vendor chunk separation.** OSMD, Tone.js, WebMIDI in their own chunks — never ship unless `/app/practice/:id` is navigated.
- **Progressive sample loading.** Lite first (≤ 5 MB); Full opt-in (≤ 150 MB) cached in IndexedDB Cache API.
- **Self-hosted fonts.** Inter + JetBrains Mono, subset to what's used, preloaded critical weights (400/500/600/700). No Google Fonts.
- **No CSS-in-JS runtime.** CSS Modules + design tokens.
- **SVG for iconography.** Tree-shakable imports from `lucide-react`.
- **No `moment.js` / large date libs.** Native `Intl.DateTimeFormat`.
- **No polyfills for evergreen browsers.** Target `>0.5%, last 2 versions, not dead`.

---

## 4. Anti-Patterns (forbidden in Phase 1)

- Importing the entirety of an icon library.
- Committing large binary fixtures (`> 100 kb`) to `apps/web/src/`.
- Inlining Salamander samples into the JS bundle.
- Shipping `node_modules` test files.
- Adding a new dependency > 50 kb gz without an ADR or PR justification.
- Using `React.lazy` without a Suspense fallback that preserves layout.

---

## 5. CI Gates

### 5.1 Lighthouse CI

Runs on every PR against the preview URL. Thresholds:

- Performance ≥ 90 (mobile) and ≥ 95 (desktop).
- Accessibility = 100.
- Best Practices ≥ 95.
- PWA = pass.

### 5.2 Bundle audit

- `vite-bundle-visualizer` diff vs. `main` on every PR.
- `bundlesize` rule on `dist/**/*.js` + `dist/**/*.css`.
- Fails if initial chunk > 200 kb gz.

### 5.3 Dependency audit

- `npm audit --audit-level=high` on every PR.
- License allowlist (MIT, BSD, Apache-2.0, ISC, CC-BY, CC0).

---

## 6. RUM Thresholds

Production RUM events that trip alerts:

- `audio.latency.p95 > 30 ms` for 10 minutes → SEV-2.
- `notation.render.ms.p95 > 1200 ms` for 30 minutes → SEV-3 investigation.
- `sample.load.bytes > 20 MB` per session → investigate (should only happen on Full opt-in).
- `midi.roundtrip.ms > 50 ms` sustained → surface Bluetooth hint.

---

## 7. Device Targets

- **Low-end reference:** 360 × 640 viewport, 4 × CPU throttle, 4G network throttle. Lighthouse CI runs this profile.
- **Desktop reference:** 1280 × 800, native CPU, cable network.

The low-end target is the constraint; if it passes, desktop passes.

---

## 8. Phase-Specific Adjustments

- **Phase 2:** expands corpus → re-evaluate Salamander Full opt-in UX and storage quota messaging.
- **Phase 3:** CRDT sync introduces new p99 latency signals → add SLO/budget rows.

---

## 9. Overrides

A PR may exceed budget **only** with:

- Explicit reason in the PR description.
- Reviewer sign-off noting the reason.
- A follow-up issue to remediate within one phase iteration.
- A note in `docs/performance/BUDGET-OVERRIDES.md` (append-only log).

Overrides are rare. The default is "fix the cause".

---

## 10. References

- `MASTER-PLAN.md §4.3`
- Lighthouse CI docs, Vite bundle visualizer
- [`../operations/SLO.md`](../operations/SLO.md)
- ADR-016 (CI/CD)

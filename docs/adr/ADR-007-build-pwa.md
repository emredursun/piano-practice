# ADR-007: Build/PWA — Vite + Workbox

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §1.1 (offline-capable), §4.2 (CI), §4.3 (bundle budget)

## Context

Phase 1 is an **offline-first, installable PWA**: once a user has loaded and licensed the app, they should be able to practice without network. This needs:
- Service Worker with precache (app shell + corpus manifest) + runtime cache (Salamander samples).
- Versioning keyed to the build hash so a deploy invalidates stale caches predictably.
- A build pipeline that enforces the §4.3 performance budget (initial JS ≤ 200 kb gz).

## Decision

**Use Vite as the build tool (already chosen in ADR-001) and Workbox as the Service Worker / PWA toolkit.**

- Workbox generates the SW at build time; SW versioned by build hash.
- Precache: app shell, corpus metadata, Lite samples (~5 MB).
- Runtime cache: full Salamander samples (on-demand, `StaleWhileRevalidate` for HTML, `CacheFirst` with expiration for samples).
- SW update flow: user-visible toast on new version; skip-waiting on user confirm.

## Consequences

- **Positive:**
  - Vite 5 HMR + source maps + `import.meta.glob` make the dev loop fast and readable.
  - Workbox's SW recipes are battle-tested; avoids the many footguns of hand-rolling a Service Worker.
  - Build-hash versioning plays well with Cloudflare Pages instant rollback (§4.2).
  - Installable PWA satisfies audit CRIT-A1/A2 expectations around offline degradation posture.
- **Negative / trade-offs:**
  - Workbox + Vite requires a specific plugin configuration; the SW lifecycle (install / waiting / active) needs thoughtful UX (update toast, skip-waiting).
  - PWA install UX varies by browser; iOS Safari is the most constrained. Phase-0 prototype validates this.
- **Neutral:**
  - Bundle-size budget enforced via Lighthouse CI and `vite-bundle-visualizer` (§4.3).

## Alternatives Considered

1. **Hand-rolled Service Worker** — rejected: high defect risk; no upside.
2. **Webpack + workbox-webpack-plugin** — rejected: Vite dev experience is materially better; no compelling reason to switch build tools.
3. **Next.js built-in PWA support** — rejected: Next.js already declined in ADR-001.

## References

- `MASTER-PLAN.md §3` (ADR-007)
- `MASTER-PLAN.md §4.3` (bundle budget)
- Workbox docs; `vite-plugin-pwa`

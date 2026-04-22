# ADR-001: Frontend — React 18 + Vite + TypeScript (strict)

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3 (index), §2.2 (scope rationale)

## Context

The v1.0 plan listed "Vite + TS + PWA" without a framework commitment. The audit flagged this as CRIT-A6: without a framework decision, component architecture, state management, testing tooling, and hiring signal all remain unresolved. Phase 1 ships real UI (dual-lane Guided + Studio, notation rendering, MIDI interaction, score-following feedback) — guessing the framework at implementation time would be irreversible and expensive.

## Decision

**Use React 18 + Vite + TypeScript in strict mode** as the Phase 1 and beyond frontend stack.

Concretely:
- React 18 with functional components and hooks; Concurrent features used only where they measurably help (e.g. `useTransition` for notation re-layout).
- Vite 5+ for dev server and production builds.
- TypeScript with `"strict": true` and `"noUncheckedIndexedAccess": true`; no `any`, no unchecked `@ts-ignore`.
- Single-page app; SSR not used (no SEO-critical content; app is interactive from first interaction).

## Consequences

- **Positive:**
  - Largest talent pool and library ecosystem, directly relevant for OSMD, WebMidi, PWA tooling.
  - Mature TypeScript integration, strong typing across hooks and context.
  - Vite's fast HMR fits the tight iterate-play-hear loop during audio/notation work.
  - Strict mode catches nullability bugs early — cheap insurance in an app where audio glitches and timing errors are hard to debug live.
- **Negative / trade-offs:**
  - React's rendering model requires care for hot paths (per-frame overlays on notation during playback). Mitigated with `React.memo`, `useMemo`, and off-React rendering where needed (Canvas/SVG outside React tree).
  - Bundle size pressure vs. the 200 kb gz initial-JS budget (§4.3). Code-splitting notation/audio/MIDI is mandatory.
- **Neutral but worth noting:**
  - Next.js was considered and rejected (no SSR needed; framework overhead not justified).
  - Svelte/Solid rejected solely on ecosystem maturity for MIDI/notation integrations at this specific moment.

## Alternatives Considered

1. **Svelte 5** — rejected: smaller ecosystem for the specific notation/MIDI libraries, thinner testing/a11y tooling than React.
2. **SolidJS** — rejected: similar ecosystem concern; unproven with OSMD/Tone.js integrations.
3. **Vue 3** — rejected: smaller available hiring pool in the TR/EU classical-music-tooling niche; no decisive technical advantage.
4. **Next.js** — rejected: adds SSR/routing overhead not needed for a single-page PWA; larger bundle.

## References

- `MASTER-PLAN.md §3` (ADR index)
- `MASTER-PLAN.md §4.3` (performance budget)
- React 18 release notes; Vite 5 docs

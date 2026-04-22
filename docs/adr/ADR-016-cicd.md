# ADR-016: CI/CD — GitHub Actions + Cloudflare Pages previews + PostHog feature flags

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §4.2 (CI/CD pipeline), closes audit CRIT-A2

## Context

Audit CRIT-A2 flagged absence of CI/CD. Shipping to a Cloudflare Pages production URL from a branch without gate checks is how regressions escape. We need a cheap, reproducible pipeline and a rollback story.

## Decision

**Use GitHub Actions as the CI orchestrator, Cloudflare Pages preview deploys per PR, production deploy on merge to `main`, and PostHog feature flags for user-facing rollouts.**

CI blocks merge on all of (§4.2):
- TypeScript strict typecheck
- ESLint zero errors, ≤ 10 warnings
- Vitest unit tests (≥ 80 % coverage on critical paths)
- Playwright E2E (auth-less critical paths)
- Lighthouse CI (Perf ≥ 90, A11y = 100, Best Practices ≥ 95, PWA pass)
- Bundle size budget (initial JS ≤ 200 kb gz, total ≤ 1 MB gz)
- `npm audit --audit-level=high`
- `gitleaks` secret scan
- License allowlist (MIT, BSD, Apache-2.0, ISC, CC-BY, CC0)
- CycloneDX SBOM attached to releases

Rollback: Cloudflare Pages instant rollback (≤ 30 s). Database migrations (Phase 2+) always reversible via forward + down scripts.

Feature flags: PostHog (free tier). Every user-facing change behind a flag with rollout percentage.

## Consequences

- **Positive:**
  - Gate checks enforce quality posture without human review at each step (solo-founder scalable).
  - Preview URLs per PR give a real deployed artefact to test — critical for audio/MIDI validation that can't be faked.
  - Feature flags enable safe progressive rollout + quick kill-switch without redeploy.
  - Directly closes audit CRIT-A2.
- **Negative / trade-offs:**
  - Full CI run time must stay reasonable (target ≤ 10 min). Parallelisation and caching required.
  - Feature flags add client-side evaluation cost; PostHog SDK kept small.
- **Neutral:**
  - Branch protection enforced on `main`; required reviews switched on at Phase 2+ when collaborators exist.

## Alternatives Considered

1. **CircleCI** — rejected: GitHub-native integration is simpler; no upside at this scale.
2. **Vercel's built-in CI** — rejected: hosting not on Vercel (ADR-008); no reason to split platforms.
3. **No feature flags** — rejected: violates §4.2 safety net; killswitch capability is non-negotiable.
4. **LaunchDarkly / Split** — rejected: cost; PostHog free tier fits Phase 1.

## References

- `MASTER-PLAN.md §3` (ADR-016)
- `MASTER-PLAN.md §4.2` (CI/CD pipeline detail)
- GitHub Actions, Cloudflare Pages, PostHog docs

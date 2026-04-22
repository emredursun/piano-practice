# ADR-015: Observability — Sentry + OpenTelemetry → Honeycomb + custom RUM

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §4.1 (observability), closes audit CRIT-A1

## Context

Audit CRIT-A1 flagged absence of observability. A browser-first product with hard latency requirements (§4.3: audio p95 ≤ 30 ms, INP p75 ≤ 200 ms) cannot be operated blind. Phase 1 has a strict free-tier budget; tooling must fit.

## Decision

**Phase 1 observability stack:**

- **Sentry** — frontend exception tracking, source-map upload via CI, release health.
- **OpenTelemetry browser SDK → Honeycomb (free tier)** — traces around `audio.init`, `notation.render`, `midi.roundtrip`, `sample.load`, `license.verify`.
- **Custom RUM events (anonymous, opt-in)** — `audio.latency.p95/p99`, `notation.render.ms`, `midi.roundtrip.ms`, `sample.load.bytes`.
- **Cloudflare Web Analytics** — privacy-preserving traffic metrics (no cookies).
- **SLOs** documented in `docs/operations/SLO.md`; burn-rate alerts (2% in 1 h OR 5% in 6 h) page the founder.

RUM is **opt-in** and **anonymous** — no user ID, no IP above /24, no precise timing per user, just aggregated percentiles.

## Consequences

- **Positive:**
  - Free-tier friendly through ~1k MAU.
  - Directly unlocks SLO-driven operations (§4.1) and closes CRIT-A1.
  - Opt-in RUM keeps privacy posture clean for KVKK/GDPR and simplifies the cookie banner.
  - OpenTelemetry is vendor-neutral — Honeycomb can be replaced later without re-instrumenting.
- **Negative / trade-offs:**
  - Three vendor integrations to maintain. Solo-founder ops burden manageable because all are passive (no live config changes in normal operation).
  - Honeycomb free tier caps event volume; acceptable at Phase-1 scale.
- **Neutral:**
  - Phase 2: Sentry Team tier as user count grows; Honeycomb Pro if signal volume requires.
  - Phase 3: Anthropic-grade observability required for EU AI Act explainability (separate ADR at that time).

## Alternatives Considered

1. **Datadog RUM** — rejected: cost; non-trivial at Phase 1.
2. **Self-hosted Grafana + Loki + Tempo** — rejected: ops burden.
3. **LogRocket** — rejected: session replay is privacy-invasive; conflicts with minimum-data posture.
4. **Only Sentry, no traces** — rejected: exception-only visibility insufficient for latency SLO operation.

## References

- `MASTER-PLAN.md §3` (ADR-015)
- `MASTER-PLAN.md §4.1` (observability + SLOs)
- Sentry, OpenTelemetry, Honeycomb docs

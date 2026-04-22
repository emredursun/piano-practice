# ADR-003: Audio — Tone.js + @tonejs/piano + Salamander Grand V3

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §4.3 (audio latency budget), §5.9 (Salamander license)

## Context

Product thesis requires **real sampled piano** — not FM synthesis, not general MIDI soundfont. The audio engine must:
- Provide low-latency (p95 ≤ 30 ms on wired MIDI, §4.3) monophonic and polyphonic playback.
- Scale from a ≤ 5 MB "Lite" initial download to a full ~150 MB on-demand sample set.
- Expose AudioWorklet-level timing for precise score-follower feedback.
- Be PD/CC-compatible so the Phase-1 license-key distribution is clean.

## Decision

**Use Tone.js as the audio scheduling and synthesis layer, `@tonejs/piano` as the sampled-piano player, and the Salamander Grand V3 sample set (CC-BY 3.0) as the timbre source.**

- Samples hosted on Cloudflare R2, served via signed URLs.
- Progressive loading: Lite ≤ 5 MB (reduced note set, single velocity layer) on first load; full set fetched on demand and cached in IndexedDB via the Cache API.
- AudioContext + AudioWorklet initialised on the first user gesture (browser autoplay policy).
- Attribution string rendered in the app footer + credited in `/legal/subprocessors`.

## Consequences

- **Positive:**
  - Battle-tested; large community; good docs.
  - AudioWorklet-backed — the only path to meet the §4.3 latency budget on modern browsers.
  - Salamander Grand V3 is a top-tier free piano sample set; expected user delight.
  - Progressive loading fits the offline-first PWA story: Lite is always cached; Full is opt-in.
- **Negative / trade-offs:**
  - Salamander full set is large (~150 MB). Strictly opt-in, cached once.
  - CC-BY 3.0 requires attribution — minor operational burden (R-09 tracks legal review).
  - iOS Safari Web Audio quirks exist; Phase-0 prototype (§2.3) explicitly validates this before Phase 1 commit.
- **Neutral:**
  - Tone.js `@tonejs/piano` internal scheduling ties us to that library's conventions; acceptable because it is well-maintained and well-documented.

## Alternatives Considered

1. **Pure Web Audio API (hand-rolled)** — rejected: we'd re-invent AudioWorklet scheduling, velocity-layer crossfading, and pedal simulation. Tone.js already solves this.
2. **SFZero / SFZ loader** — rejected: less mature in browser; larger memory footprint; our latency budget is tighter.
3. **Synthesized piano (FM / additive)** — rejected: contradicts the product thesis (real sampled piano, not a stylized tone).
4. **Commercial sample libraries (Pianobook, Native Instruments)** — rejected: license terms incompatible with SaaS distribution; cost prohibitive at Phase-1 revenue.

## References

- `MASTER-PLAN.md §3` (ADR-003)
- `MASTER-PLAN.md §4.3` (audio latency p95 ≤ 30 ms wired MIDI)
- `MASTER-PLAN.md §5.9` (Salamander license compliance)
- Salamander Grand V3 (CC-BY 3.0); Tone.js; `@tonejs/piano`

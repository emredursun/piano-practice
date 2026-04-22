# ADR-004: MIDI — WebMidi.js (input) + @tonejs/midi (file parse)

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §4.3 (MIDI roundtrip budget)

## Context

Phase 1 requires **real MIDI input** (user plays their keyboard, app score-follows in real time) and MIDI file parsing (reference recordings, exercise generation). Raw Web MIDI API is usable but low-level — port enumeration, device hot-plug, SysEx handling, and timing normalization would otherwise be hand-rolled.

## Decision

**Use `WebMidi.js` for live MIDI input and device management; use `@tonejs/midi` for MIDI file parsing and authoring.**

- `SysEx: false` by default (principle of least privilege; avoids firmware-interaction attack surface).
- Device hot-plug observed via `WebMidi.js` events; UI reflects the connected device list in real time.
- MIDI roundtrip (input → score-follower match → audio echo) targeted at p95 ≤ 30 ms (§4.3) with wired devices; Bluetooth MIDI noted as best-effort with clear UX warning.

## Consequences

- **Positive:**
  - `WebMidi.js` normalizes cross-browser quirks; active maintenance.
  - `@tonejs/midi` interoperates cleanly with Tone.js audio scheduling (ADR-003).
  - Declarative device events simplify the UI's "connect your keyboard" onboarding.
- **Negative / trade-offs:**
  - Web MIDI not available on all browsers (Firefox historically limited). Mitigated: clear UX message + alternative virtual MIDI keyboard in-app for users without hardware.
  - Bluetooth MIDI latency is inherently higher (≥ 15–40 ms typical); we cannot meet §4.3 on Bluetooth. UX warning will surface this.
- **Neutral:**
  - `@tonejs/midi` is not a replacement for a fully-featured DAW; sufficient for our purposes (reference recordings, exercise files).

## Alternatives Considered

1. **Raw Web MIDI API** — rejected: reinventing event normalization, hot-plug handling, and port abstraction; no benefit.
2. **MidiConvert (older sibling of `@tonejs/midi`)** — rejected: superseded by `@tonejs/midi`.
3. **Custom MIDI parser** — rejected: substantial complexity, no upside over `@tonejs/midi`.

## References

- `MASTER-PLAN.md §3` (ADR-004)
- `MASTER-PLAN.md §4.3` (MIDI roundtrip budget)
- `WebMidi.js`, `@tonejs/midi`

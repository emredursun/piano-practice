# Interaction Patterns — Piano-Practice

> **Scope:** How the user interacts with the core features — notation, audio, MIDI, practice controls, license entry.
> **Authority:** ADR-002 (OSMD), ADR-003 (Tone.js audio), ADR-004 (WebMidi.js), ADR-005 (fingering), ADR-006 (state), [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md).

---

## 1. Practice Session — Core Loop

### 1.1 Lifecycle states

```
mounted → preparing → audio-locked → ready → playing → (paused | wrong-note | completed) → summary
                                          ↑__________↓
```

- **preparing** — loading MusicXML, initialising OSMD, fetching samples.
- **audio-locked** — waiting for first user gesture (browser autoplay policy).
- **ready** — audio context live, samples cached at least to Lite, MIDI device enumerated.
- **playing** — score-follower active, audio scheduler running.
- **paused** — scheduler paused; cursor holds position.
- **wrong-note** — transient highlight on missed note; does not halt playing (configurable).
- **completed** — final bar reached; transitions to summary after 800 ms.

### 1.2 Audio unlock

- **Single prominent Start button** on `audio-locked` state — full-width on `xs/sm`, centered card on `md+`.
- Pressing Start:
  1. Calls `Tone.start()` inside the event handler (not async-delayed) — satisfies autoplay policy.
  2. Plays a 200 ms A4 test tone (confirms audio path is alive, not silent).
  3. Transitions to `ready`.
- Fallback: if `Tone.start()` rejects, show `AudioUnlockError` with troubleshoot hints (headphones, volume, browser settings).

### 1.3 MIDI connection

- Permission requested **implicitly** on first `Tone.start` (combined gesture: user clicks Start → we request MIDI + audio together).
- If permission denied → `ready` is still entered, but `MidiStatusPill` shows "Playback-only (tap to re-enable MIDI input)".
- Device enumeration live-updated via `WebMidi.onAdd` / `onRemove` events; pill updates without page refresh.

### 1.4 Score-follower interaction

Matching rule: **50 ms chord aggregation window**.

- User presses a note → within 50 ms, any subsequent notes bond into the same chord-match attempt.
- Correct (all notes of current beat hit): advance cursor; render success overlay.
- Partial (subset): render ambiguity overlay (neutral colour); stay on beat.
- Wrong (unexpected note): brief red pulse at the offending note; stay on beat.

**User control:**
- **Section loop A/B**: user presses `[` at current beat to set A; `]` to set B; `Esc` clears.
- **Tempo ramp**: slider 50–120 % of reference tempo; live adjustment.
- **Hands separate**: toggle left-hand-only / right-hand-only / both (Studio lane only).
- **Blind play**: notation hidden below cursor position (Studio lane only; pedagogical).

---

## 2. MIDI Input Handling

### 2.1 Device lifecycle

- `navigator.requestMIDIAccess({ sysex: false })` on audio unlock.
- WebMidi.js wraps the MIDIAccess; we subscribe to `stateChange`.
- Devices are opened lazily on first use.

### 2.2 Event handling

- Only `noteOn` (velocity > 0) triggers score-follower input.
- `noteOn` with velocity = 0 is a `noteOff` — passed to audio envelope release only.
- Aftertouch, pitch bend, control change → logged for Phase 3, ignored in Phase 1 score-follower.
- Sustain pedal (CC64) → passed to audio engine sustain; does not affect score match.

### 2.3 Latency measurement

- `noteOn` event has `event.timestamp` (performance.now() relative).
- First audible output has a logged `AudioContext.currentTime` at scheduled playback.
- Delta logged to `latency-probe`; p95 surfaced on `LatencyBadge` live.
- If `LatencyBadge` shows p95 > 30 ms for 10 s, render a dismissible warning toast with a diagnostic link.

### 2.4 Bluetooth MIDI caveat

- Browser reports BLE MIDI as a standard device — we cannot detect transport cleanly.
- `LatencyBadge` showing p95 > 15 ms *likely* indicates BLE.
- One-shot hint on first session: "Bluetooth MIDI adds latency — wired USB is recommended."

---

## 3. Audio Engine Interaction

### 3.1 Sample loading

- **Lite set (5 MB)** loaded on first session; cached in Cache Storage API.
- **Full set (150 MB)** only on explicit opt-in from Settings → Audio.
- Progress reported via `sample.load.bytes` RUM event; UI progress bar visible during load.

### 3.2 Playback routing

- Piano via `@tonejs/piano`.
- MIDI-in echoed directly to piano (no roundtrip latency penalty for user-generated notes).
- Reference MIDI (for "listen" mode in Studio) scheduled via `Tone.Transport`.

### 3.3 Volume & mix

- Global volume slider 0–100 % with `dB`-curved mapping (perceptual).
- Mute button (`M`) toggles zero gain without destroying state.
- **No separate reference-vs-user mix in Phase 1** (Phase 2 adds backing-track vs. player mix).

### 3.4 Audio context quirks

- iOS Safari: context suspends after ~60 s of silence; resume on next gesture.
- Firefox: AudioWorklet requires secure context (served over HTTPS — always is in production).
- Chrome: `latencyHint: "interactive"` requested.

---

## 4. Notation Interaction (OSMD)

### 4.1 Rendering

- `OpenSheetMusicDisplay` mounted to a `<div>` ref; re-renders on piece change or layout reflow.
- Re-layout debounced 200 ms on window resize.
- Reduced-motion: disable fade-in of notation overlays.

### 4.2 Cursor

- OSMD internal cursor API drives visual position.
- Our `ScoreCursor` wraps it; advances on successful score-follower match.
- Auto-scroll: ensures cursor stays within top 30 % of viewport during playing state.

### 4.3 Selection & loop

- Click-and-drag on bars (desktop) or tap-to-set-A / tap-to-set-B (mobile) to define loop bounds.
- Selected bars render with `--color-accent` ring.
- Clear loop via `Esc` (desktop) or dismiss chip (mobile).

### 4.4 Zoom

- Native pinch-zoom respected — OSMD SVG is first-class zoomable.
- A `Ctrl +` / `Ctrl -` keyboard shortcut scales the OSMD render at fixed ratios (0.8, 1.0, 1.25, 1.5).

### 4.5 Fingering overlay

- Three toggleable layers (Settings → Audio or piece-level menu):
  - **None** — plain score.
  - **Editorial (Czerny/Busoni)** — PD reference fingerings.
  - **AI (Parncutt via pianoplayer)** — algorithmic suggestion (ADR-005).
- Overlays are SVG `<text>` anchored to OSMD note positions.
- User overrides persist to Dexie per piece per user.

---

## 5. Keyboard Shortcuts

| Shortcut | Action | Screen |
|---|---|---|
| `Space` | Play / pause | practice |
| `.` / `,` | Set loop B / A | practice |
| `Esc` | Clear loop | practice |
| `[` / `]` | Tempo −5 / +5 BPM | practice |
| `h` | Toggle hands | practice (Studio) |
| `b` | Toggle blind play | practice (Studio) |
| `f` | Cycle fingering layer | practice |
| `r` | Restart bar | practice |
| `Ctrl +` / `-` | Zoom notation | practice |
| `M` | Mute | practice |
| `g s` | Go to settings | anywhere in `/app` |
| `g l` | Go to library | anywhere in `/app` |
| `?` | Show shortcuts overlay | anywhere |

All shortcuts rendered in the `?` overlay and in Settings → Appearance.

---

## 6. Error & Recovery Patterns

### 6.1 Principles

1. **Never silent.** Every failure surfaces a user-visible state.
2. **Actionable.** Error messages include a next step, not just a name.
3. **Recoverable where possible.** Retry, re-request permission, re-load sample.
4. **Logged.** Sentry captures; Honeycomb gets a span.

### 6.2 Canonical error families

| Family | Examples | UI | Recovery |
|---|---|---|---|
| Audio | Context suspended, sample load failure | toast + Start button | Re-click Start |
| MIDI | Permission denied, device disconnected | MidiStatusPill warning + dialog | Re-grant permission |
| Network | Webhook verification timeout, license lookup | inline form error + retry | Retry, manual license entry |
| License | Invalid, revoked, expired version | screen-level block + help link | Re-unlock, recover via email |
| Notation | OSMD render exception, bad MusicXML | screen-level block + "Report" | Reload, Sentry event surfaced |
| Storage | IndexedDB quota hit | toast + export-suggest | Export JSON, clear space |

See [`UX-COPY.md`](UX-COPY.md) for the exact strings.

---

## 7. Feedback Patterns

### 7.1 Immediate

- Visual note match / mismatch on the score (non-blocking).
- Toast for transient events (mute, rest-restored, loop cleared).

### 7.2 Batched

- Screen reader announcements aggregated per bar (not per note).
- RUM events sampled (not every keystroke).

### 7.3 Summative

- Session summary on completion.
- 90-day practice heatmap on Library card (Phase 2 fleshed-out).

### 7.4 What we deliberately avoid

- No confetti. No "Level up!" flashes. No streak guilt-tripping.
- No sound effects on UI clicks (noise in a music app is wrong).
- No progress-shaming push notifications.

---

## 8. Form Patterns

### 8.1 License entry

- Auto-focus input on mount.
- Paste detection: if a paste event yields ≥ 40 chars, auto-validate (don't require button click).
- Show validation as green check or red error inline.
- Never clear the field on error (preserve user's paste).

### 8.2 Email capture (Phase 0 landing)

- Double opt-in per GDPR (email service's flow).
- Country hint via `cf-ipcountry` to default locale.
- Rate-limited: 3 submits per email per hour (email-service-level).

### 8.3 Settings persistence

- Auto-save on change; small "Saved" toast after 500 ms debounce.
- If save fails (IndexedDB quota), preserve in-memory change and show a banner.

---

## 9. Loading Patterns

### 9.1 Skeletons vs. spinners

- **Skeletons** on route change (library, piece detail) — preserve layout, reduce CLS.
- **Spinners** on explicit async button presses (Start, Export, Checkout redirect).
- **Progress bars** on measurable operations (sample load, export size known).

### 9.2 Minimum visible time

- Skeletons visible at least 200 ms (prevents flash of skeleton + flash of content).
- Spinners appear after 400 ms (if operation completes before then, no spinner at all).

### 9.3 Stale-while-revalidate

- Service Worker serves cached shell instantly; background-fetches fresher version.
- In-app banner on new SW version: "Update available — reload to apply" (user-controlled).

---

## 10. Localisation Interaction

- **`<html lang>`** updated on locale change; no page reload.
- Number formatting per-locale (`Intl.NumberFormat`).
- Date formatting per-locale (`Intl.DateTimeFormat`).
- Pluralisation via ICU MessageFormat (library TBD at Phase-1 Week 4; strong candidates: `@formatjs/intl` or `format-message`).
- TR labels never clip; components tested with longest-locale fixture.

---

## 11. Motion & Haptics

- Motion tokens in [`DESIGN-SYSTEM.md §6`](DESIGN-SYSTEM.md).
- Reduced-motion respected globally.
- Haptics (iOS WebKit `Taptic Engine`, Android `Vibration API`) — **unused in Phase 1**.

---

## 12. References

- [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md) — tokens and components
- [`ACCESSIBILITY.md`](ACCESSIBILITY.md) — SR announcements, keyboard, focus management
- [`UX-COPY.md`](UX-COPY.md) — strings for every state above
- [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md) — breakpoints, device capability matrix
- ADR-002, ADR-003, ADR-004, ADR-005

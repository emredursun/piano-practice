# Responsive Strategy — Piano-Practice

> **Scope:** Web PWA, mobile-first, installable on iOS / Android / desktop. No native mobile app in Phase 1.
> **Authority:** `MASTER-PLAN.md §1.1` (browser-first, offline-capable), ADR-007 (Workbox PWA), [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md).

---

## 1. Strategy Summary

Piano-Practice ships as **one responsive web PWA**. Not two codebases, not one desktop + one mobile build. The same URL, the same JavaScript, the same feature set — adapted per viewport, input modality, and platform capability.

**Mobile-first:** base CSS is mobile; progressive enhancement adds desktop affordances.
**Progressive enhancement, not progressive degradation:** feature-detect before feature-forbid.
**No-regret fallbacks:** if Web MIDI or AudioWorklet is missing, render a helpful state — never a blank screen.

---

## 2. Breakpoint System

Breakpoints are named by **capability**, not device (device-based naming ages badly).

| Token | min-width | Archetype | Default behaviour |
|---|---|---|---|
| `--bp-xs` | 0 | small phone portrait | one column, stacked, sticky bottom bar |
| `--bp-sm` | 480 px | large phone portrait / small phone landscape | one column, denser padding |
| `--bp-md` | 768 px | tablet portrait | two-column library, modal instead of sheet |
| `--bp-lg` | 1024 px | tablet landscape / laptop | Studio-lane two-pane layout unlocked |
| `--bp-xl` | 1440 px | large laptop / desktop | generous whitespace, wider score pane |
| `--bp-2xl` | 1920 px | external monitor | cap content width; don't over-stretch |

Implementation: CSS `@media (min-width: ...)` only (mobile-first).

---

## 3. Layout Modes

### 3.1 Practice session (the critical screen)

| Breakpoint | Layout | Score area | Controls |
|---|---|---|---|
| `xs`–`sm` | Stacked | Top 60 vh | Sticky bottom 40 vh sheet |
| `md` | Stacked, wider | Top 65 vh | Sticky bottom 35 vh sheet |
| `lg`+ | Two-pane | Left 70 % | Right-dock 30 % panel |

**Landscape phone** is treated as a `sm`-width case — still stacked. The score gets the top half; controls bottom. Rotating to landscape does not auto-enter two-pane (too narrow for useful two-pane).

### 3.2 Library

| Breakpoint | Columns | Card size |
|---|---|---|
| `xs` | 1 | Full width |
| `sm` | 2 | |
| `md` | 3 | |
| `lg`–`xl` | 4 | |
| `2xl` | 4 (capped) | |

### 3.3 Settings

- `xs`–`sm`: accordion (collapsed categories).
- `md`+: left-nav tabs.

### 3.4 Legal pages

Always single column, max-width `65ch` for readability. No breakpoints change this.

---

## 4. Input Modality

### 4.1 Touch vs. mouse vs. keyboard

Detection: **capabilities, not breakpoints.**

- `@media (hover: hover) and (pointer: fine)` → desktop affordances (hover tooltips, denser targets).
- `@media (hover: none) and (pointer: coarse)` → touch affordances (≥ 44 × 44 targets, no hover).
- Keyboard users are a first-class set on every breakpoint — shortcuts visible in `?` overlay.

### 4.2 Touch-target minimum

**≥ 44 × 44 CSS px** on any breakpoint where touch is plausible. Apple HIG minimum; WCAG 2.5.5 recommended.

### 4.3 Gesture reservation

- **Swipe left/right** in library: Phase 2 "next piece" — unused Phase 1, do not intercept.
- **Pinch-zoom on score**: browser-native, do not hijack.
- **Long-press**: reserved for Phase-2 context menu; Phase 1 ignores.
- **Two-finger scroll on score**: browser default vertical scroll.
- **Tap-and-hold note**: reserved for Phase-3 fingering override.

---

## 5. Platform Capability Matrix

| Feature | Chrome/Edge (desktop) | Chrome (Android) | Safari (macOS) | Safari (iOS/iPadOS) | Firefox (desktop) |
|---|---|---|---|---|---|
| Web MIDI | ✅ | ✅ | ⚠ with flag | ❌ | ⚠ flag |
| AudioWorklet | ✅ | ✅ | ✅ | ✅ (iOS 14+) | ✅ |
| PWA install (A2HS) | ✅ prompt | ✅ prompt | ⚠ manual | ⚠ manual Add to Home Screen | ⚠ manual |
| Service Worker cache | ✅ | ✅ | ✅ | ⚠ 7-day eviction quota | ✅ |
| IndexedDB | ✅ | ✅ | ✅ | ✅ | ✅ |
| Cache Storage API | ✅ | ✅ | ✅ | ✅ | ✅ |
| Clipboard API (license paste) | ✅ | ✅ | ✅ | ✅ | ✅ |
| Autoplay policy | gesture-required | gesture-required | gesture-required | gesture-required, strict | gesture-required |

**Implication for Phase 1:**
- iOS = audio-playback-only (no MIDI input). Copy states this on device detection.
- Firefox WebMIDI: treat as best-effort; show warning.
- PWA install prompt: platform-specific copy in onboarding (iOS needs a visual guide; Android/desktop gets the standard prompt).

Phase-0 prototype explicitly validates this matrix (see plan WS4-T09).

---

## 6. Orientation

- **Portrait (phone)**: primary layout; sticky bottom bar.
- **Landscape (phone)**: score takes top 55 vh; controls bottom — but phone landscape is a secondary use case, not optimised.
- **Portrait (tablet)**: same as phone `md`.
- **Landscape (tablet)**: unlock two-pane at `lg` — ideal practice experience on tablet.
- **Orientation lock:** never force. Users may have accessibility reasons to lock.

---

## 7. Viewport Meta

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
```

- **No** `maximum-scale` (would block pinch-zoom; WCAG violation).
- **`viewport-fit=cover`** for iOS notch/safe-area support.
- **`env(safe-area-inset-*)`** used in sticky bottom bar to avoid notch / home-indicator overlap.

---

## 8. PWA Install & Offline

### 8.1 Install prompt

- Detected via `beforeinstallprompt` (Chromium family).
- **Never auto-show on first visit.** Trigger: second visit OR after completing a practice session (higher intent moment).
- iOS: custom in-app card explains "Add to Home Screen" with screenshots; opt-in dismiss.

### 8.2 Offline behaviour

- App shell precached on install.
- Corpus metadata precached (small).
- Samples: fetched on first piece-play; cached via Cache Storage API; Lite (5 MB) persists, Full (150 MB) only on explicit opt-in.
- If a non-cached route is requested offline → redirect to `/offline` (see [`SCREENS-INVENTORY.md §3.17`](SCREENS-INVENTORY.md)).
- Offline banner shown globally when `navigator.onLine === false` (dismissible per session).

### 8.3 iOS storage quirks

- iOS Safari aggressively evicts caches at 7 days of non-use.
- Warn users in Settings → Data: "Visit every 6 days or export JSON to avoid iOS evicting cached pieces."

---

## 9. Performance Budgets per Breakpoint

From `MASTER-PLAN.md §4.3`:

| Metric | `xs`–`sm` (4G mid-tier) | `lg+` (Wi-Fi) |
|---|---|---|
| LCP p75 | ≤ 2.5 s | ≤ 1.5 s |
| INP p75 | ≤ 200 ms | ≤ 150 ms |
| CLS | ≤ 0.1 | ≤ 0.1 |
| Initial JS gz | ≤ 200 kb | (same bundle) |

Lighthouse CI runs against `xs` (mobile 4G preset) and `lg` (desktop preset). Both must pass.

---

## 10. Mobile-Specific UX Details

- **Score zoom**: pinch-zoom on OSMD SVG — browser-native, not hijacked.
- **Auto-scroll during playback**: enabled; respects `prefers-reduced-motion`.
- **Sticky bottom controls**: always visible during practice; `position: sticky; bottom: env(safe-area-inset-bottom);`.
- **Haptics**: intentionally unused in Phase 1 (cost/benefit unclear; consider Phase 2).
- **Battery awareness** (`navigator.getBattery`): if available and `level < 0.2 && !charging`, offer lower-quality sample mode (Lite only).

---

## 11. Desktop-Specific UX Details

- **Two-pane studio** at `lg+` only.
- **Hover affordances** (tooltips, subtle highlights) gated by `@media (hover: hover)`.
- **Keyboard shortcuts** are a primary interaction model — documented in `?` overlay.
- **Window resize**: layout reflows live; score re-layout debounced 200 ms to avoid OSMD thrashing.

---

## 12. Testing Matrix

| Device class | Viewport | OS / Browser | Cadence |
|---|---|---|---|
| Small phone | 360 × 640 | Chrome Android | per PR (Playwright) |
| Reference phone | 390 × 844 | Safari iOS 17+ | manual weekly |
| Reference tablet | 820 × 1180 | Safari iPadOS | manual weekly |
| Laptop | 1280 × 800 | Chrome macOS | per PR |
| Desktop | 1920 × 1080 | Edge Windows | per PR |
| Low-end | 360 × 640 + throttled 4G CPU 4× | Chrome DevTools | per PR |

Phase-0 prototype runs this matrix as WS4-T09.

---

## 13. Known Non-Goals (Phase 1)

- Native iOS/Android app. **No.** PWA is the distribution.
- Tauri/Electron desktop wrapper. **No** (unless Phase-0 G1 uncovers an unrecoverable web blocker — then re-scope per §2.3).
- Foldable device optimisation. **No** in Phase 1; revisit Phase 3.
- TV / large-screen remote navigation. **No.**

---

## 14. References

- [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md) — tokens and components
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md) — practice controls
- [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md) — per-screen responsive notes
- `MASTER-PLAN.md §4.3` (performance budget)
- ADR-007 (PWA via Workbox)

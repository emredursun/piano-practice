# Wireframes — Piano-Practice (Phase 1)

> **Scope:** Low-fidelity ASCII/Mermaid wireframes for the key screens in [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md). Intentionally coarse — they describe structure and hierarchy, not pixel design. High-fidelity mocks are produced during Phase-1 Week 4 in Figma (or direct React prototyping).
> **Authority:** [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md), [`INFORMATION-ARCHITECTURE.md`](INFORMATION-ARCHITECTURE.md), [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md).

---

## Legend

- `[ Button ]` = interactive button
- `< Link >` = anchor
- `( o ) selected` = radio/chip
- `[✓] label` = checkbox/switch
- `{{ placeholder }}` = dynamic content
- `· · ·` = overflow menu
- `▒` = sample image / icon placeholder

---

## 1. `/` Landing — Mobile (xs)

```
┌──────────────────────────────┐
│ ≡  piano-practice     ·$·    │ ← top bar
├──────────────────────────────┤
│                              │
│  Practise Bach on a real     │ ← hero h1
│  piano. In your browser.     │
│                              │
│  Real engraved notation.     │
│  Real sampled Steinway.      │
│  Real MIDI.                  │
│                              │
│  [ Buy lifetime — $29 ]      │ ← primary CTA
│  < 14-day refund guarantee > │
│                              │
│  ▒  ▒  ▒                     │ ← 3 feature icons
│  Score-follower              │
│  AI fingering                │
│  Offline PWA                 │
│                              │
│  Available in Türkiye & EU.  │
│  Minimum age 16.             │
│                              │
├──────────────────────────────┤
│ < Privacy · Terms · A11y >   │
└──────────────────────────────┘
```

## 1b. `/` Landing — Desktop (lg+)

```
┌──────────────────────────────────────────────────────────────────┐
│  piano-practice       How it works · Pricing · FAQ   < Unlock >  │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Practise Bach on a real piano.    │   ▒ Score preview          │
│   In your browser.                  │   ▒ (animated SVG)         │
│                                     │                            │
│   [ Buy lifetime — $29 ]            │                            │
│   < 14-day refund guarantee >       │                            │
│                                     │                            │
├──────────────────────────────────────────────────────────────────┤
│   ▒ Score-follower       ▒ AI fingering       ▒ Offline PWA      │
├──────────────────────────────────────────────────────────────────┤
│   FAQ accordion, testimonials (Phase 2), footer                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. `/unlock` — Mobile & Desktop (single column)

```
┌──────────────────────────────┐
│  piano-practice              │
├──────────────────────────────┤
│                              │
│  Enter your license          │ ← h1
│                              │
│  License code                │ ← label
│  ┌──────────────────────────┐│
│  │ pp-                      ││ ← input (auto-focus)
│  └──────────────────────────┘│
│  < Paste from email >        │ ← paste helper
│  ⚠ That doesn't look valid.  │ ← aria-live error slot
│                              │
│  [ Unlock ]                  │
│                              │
│  < Can't find your license? >│
│                              │
└──────────────────────────────┘
```

---

## 3. `/app` Library — Mobile & Desktop

### Mobile (xs)

```
┌──────────────────────────────┐
│ ← Library      [MIDI ●] [⚙] │ ← top
├──────────────────────────────┤
│                              │
│  ▶ Resume                    │ ← last-practised card
│  ┌──────────────────────────┐│
│  │ BWV 774 · Invention No.3 ││
│  │ 2 days ago · 87 %        ││
│  │ [ Practise ]             ││
│  └──────────────────────────┘│
│                              │
│  Pieces (15)                 │
│  ┌──────────────────────────┐│
│  │ BWV 772 · No.1 · C major ││
│  │ Grade 5 · 21 bars        ││
│  └──────────────────────────┘│
│  ┌──────────────────────────┐│
│  │ BWV 773 · No.2 · c minor ││
│  │ Grade 5 · 24 bars        ││
│  └──────────────────────────┘│
│  ...                         │
│                              │
├──────────────────────────────┤
│ [ ▒ Library ] [ ▒ Settings ] │ ← bottom nav
└──────────────────────────────┘
```

### Desktop (lg+)

```
┌─────────────────────────────────────────────────────────────┐
│ piano-practice      Library   Settings    [MIDI ●] Latency  │
├─────────────────────────────────────────────────────────────┤
│ ┌───────────┐                                               │
│ │ ▒Library  │   Resume                                      │
│ │ ▒Settings │   ┌──────────────┐                            │
│ └───────────┘   │ BWV 774 … 87%│                            │
│                 └──────────────┘                            │
│                                                             │
│                 Pieces (15)                                 │
│                 ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐             │
│                 │BWV  │ │BWV  │ │BWV  │ │BWV  │             │
│                 │ 772 │ │ 773 │ │ 774 │ │ 775 │             │
│                 └─────┘ └─────┘ └─────┘ └─────┘             │
│                 ...                                         │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. `/app/practice/:id` — Guided Lane

### Mobile stacked (xs–sm)

```
┌──────────────────────────────┐
│ ← BWV 772    [●Guided Studio]│ ← lane toggle
├──────────────────────────────┤
│                              │
│   ▒ OSMD score pane          │
│   bar 1 · · · · ♩            │
│   bar 2 · · · · ♩            │
│   bar 3 · · · · ♩    (scrl ↓)│
│                              │
├──────────────────────────────┤
│ ♩=96  [ ▶ Start ]  [ A─B ] · │ ← practice bar (sticky)
│ ────●──────  tempo 100 %     │
└──────────────────────────────┘
         [ MIDI ● · 18 ms ]
```

### Desktop two-pane (lg+)

```
┌───────────────────────────────────────────────────────────┐
│ ← BWV 772 in C major     [●Guided · Studio]    [?] [·]   │
├───────────────────────────────────────────────────────────┤
│                                     │                     │
│   ▒ OSMD score pane                 │  Practice           │
│   (70% width)                       │                     │
│   bar 1 · · · · ♩                   │  [ ▶ Start ]        │
│   bar 2 · · · · ♩                   │  Tempo  ───●─── 100%│
│   bar 3 · · · · ♩                   │  Loop   A ⟶ B      │
│   bar 4 · · · · ♩                   │  Hands  [Both]      │
│                                     │  Fingers [AI ∙ Ed.] │
│                                     │                     │
│                                     │  MIDI ●  ·  18 ms   │
├───────────────────────────────────────────────────────────┤
│ < Bar 4 — 3/4 correct so far >       (SR live-region)    │
└───────────────────────────────────────────────────────────┘
```

### Audio-locked overlay (any viewport)

```
   ┌────────────────────────────────┐
   │                                │
   │   Tap Start to enable          │
   │   audio and MIDI               │
   │                                │
   │   [    ▶  Start    ]           │
   │                                │
   │   Your browser waits for your  │
   │   first action before making   │
   │   sound.                       │
   │                                │
   └────────────────────────────────┘
```

---

## 5. `/app/session/:id/summary`

```
┌──────────────────────────────┐
│ ← Session complete           │
├──────────────────────────────┤
│                              │
│  BWV 772 · 16 bars           │
│  87 % accuracy               │
│  12 min 34 sec                │
│                              │
│  Accuracy by bar:            │
│  ▓▓▓▓▓▓▓▓▓▓▓▓▒▒▒▒            │ ← mini bar chart
│                              │
│  How did it feel?  (Phase 2  │
│  SRS rating — Phase 1 noop)  │
│  [Again] [Hard] [Good] [Easy]│
│                              │
│  [ Practise again ]          │
│  < Back to library >         │
│                              │
│  90-day heatmap:             │
│  ▓▓░░░▓▓▓░░▓▓▓▓▓░░░▓         │
│                              │
└──────────────────────────────┘
```

---

## 6. `/app/settings`

### Mobile — accordion

```
┌──────────────────────────────┐
│ ← Settings                   │
├──────────────────────────────┤
│ ▶ Audio                      │ ← collapsed
│ ▶ MIDI                       │
│ ▼ Appearance                 │ ← expanded
│     Theme   ( o Dark  · Light│
│              · Auto )        │
│     Language ( o English · TR│
│     Reduced motion [✓]       │
│ ▶ Language                   │
│ ▶ Data                       │
└──────────────────────────────┘
```

### Desktop — left-nav tabs

```
┌───────────────────────────────────────────────┐
│ ← Settings                                    │
├───────────────────────────────────────────────┤
│ ┌──────────┐                                  │
│ │ Audio    │   Theme     ( o Dark · Light )   │
│ │ MIDI     │   Language  ( o EN · TR )        │
│ │▶Appear.◀ │   [✓] Reduced motion             │
│ │ Language │                                  │
│ │ Data     │                                  │
│ └──────────┘                                  │
└───────────────────────────────────────────────┘
```

---

## 7. `/app/settings/export`

```
┌──────────────────────────────┐
│ ← Export / Import            │
├──────────────────────────────┤
│                              │
│  Your practice history lives │
│  only on this device.        │
│                              │
│  ┌──────────────────────────┐│
│  │ ⬇  Export my data (JSON) ││
│  │  ~ 42 KB                  ││
│  └──────────────────────────┘│
│                              │
│  ┌──────────────────────────┐│
│  │ ⬆  Import from JSON      ││
│  │  Replaces current history.││
│  └──────────────────────────┘│
│                              │
│  ⚠ Samples aren't exported — │
│    they re-download on first │
│    play after import.        │
│                              │
└──────────────────────────────┘
```

---

## 8. `/offline` — SW Fallback

```
┌──────────────────────────────┐
│  You're offline              │
├──────────────────────────────┤
│                              │
│  Don't worry — you can still │
│  practise pieces you've      │
│  played before.              │
│                              │
│  Cached pieces:              │
│  • BWV 772                   │
│  • BWV 774                   │
│  • BWV 775                   │
│                              │
│  [ Go to Library ]           │
│                              │
│  Everything else will load   │
│  as soon as you reconnect.   │
│                              │
└──────────────────────────────┘
```

---

## 9. Empty / Error Fallbacks

### `/404`

```
┌──────────────────────────────┐
│                              │
│  404 — Page not found        │
│                              │
│  The page you asked for      │
│  doesn't exist.              │
│                              │
│  [ Go home ]                 │
│                              │
└──────────────────────────────┘
```

### `ErrorBoundary` fallback

```
┌──────────────────────────────┐
│                              │
│  Something didn't load       │
│  properly.                   │
│                              │
│  Error ID: {{ sentry-event }}│
│                              │
│  [ Reload ] [ Report ]       │
│                              │
└──────────────────────────────┘
```

---

## 10. `/sprint0/landing` (disposable — Phase 0 only)

```
┌──────────────────────────────┐
│  Practise Bach in your       │
│  browser — coming soon.      │
├──────────────────────────────┤
│                              │
│  ▒ 30-sec demo (muted; tap   │
│    to unmute)                │
│                              │
│  Email me when it launches:  │
│  ┌──────────────────────────┐│
│  │ you@example.com          ││
│  └──────────────────────────┘│
│  [ Notify me ]               │
│                              │
│  Would you pay $29 for       │
│  lifetime access?            │
│  ( Yes · Maybe · No )        │
│                              │
│  ─────────────────────────── │
│  Only in Türkiye and EU.     │
│  Minimum age 16.             │
│  < Privacy >  < Terms >      │
└──────────────────────────────┘
```

---

## 11. Modal / Sheet Pattern

### Mobile — bottom sheet

```
┌──────────────────────────────┐
│                              │
│                              │ ← page (dimmed)
│                              │
├──────────────────────────────┤ ← grab handle
│ ─                            │
│ Modal title                  │
│                              │
│ Modal body                   │
│                              │
│ [ Primary ]  [ Cancel ]      │
└──────────────────────────────┘
```

### Desktop — centered dialog

```
                ┌──────────────────┐
                │ Modal title      │
                │                  │
                │ Modal body       │
                │                  │
                │ [ Cancel ]  [OK] │
                └──────────────────┘
```

---

## 12. High-fidelity plan

Phase-1 Week 4: translate these wireframes into Figma (or straight React+Storybook):

1. DESIGN-SYSTEM tokens instantiated in Figma Variables (or a `tokens.css` first-party).
2. Each screen wireframe reproduced at `xs` + `lg` fidelity.
3. A11y review against [`ACCESSIBILITY.md`](ACCESSIBILITY.md) before engineering begins.
4. Prototype interactions for: audio unlock, score-follower cursor, wrong-note feedback.

No Figma license is required for Phase 1 if we go straight-to-code with Storybook — decision captured in Phase-1 Week 4 ADR.

---

## 13. References

- [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md)
- [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md)
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md)
- [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md)
- [`UX-COPY.md`](UX-COPY.md)

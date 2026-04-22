# Design System — Piano-Practice

> **Scope:** Phase 1 (MVP). Mobile-first, responsive web PWA.
> **Authority:** `MASTER-PLAN.md §1.1` (dual-mode UI), ADR-001 (React + Vite + TS), `SECURITY.md` (CSP), `ACCESSIBILITY.md` (WCAG 2.1 AA).
> **Status:** Foundational. Amendments require a visual review + a11y sign-off.

---

## 1. Design Principles

1. **Notation is the hero.** Everything frames the score; UI chrome recedes.
2. **Beginners deserve dignity.** "Guided lane" reduces cognitive load without infantilising. No cartoons, no confetti.
3. **Experts deserve control.** "Studio lane" exposes parameters; no forced hand-holding.
4. **Silent failure is a bug.** Every error has a visible, actionable recovery path.
5. **Keyboard + screen reader are first-class.** Not retro-fitted, not second-class.
6. **Dark mode is default.** Music practice often happens in low light; OLED-friendly ink economy.
7. **Offline-first UX.** App tells the user what's cached and what isn't; no mystery blank screens.
8. **TR and EN are equal citizens.** No label is English-first; both locales ship together.

---

## 2. Brand Primitives

See [`BRAND.md`](BRAND.md) for brand voice, logo, and marketing surface. This file covers the **product surface** only.

---

## 3. Color Tokens

All tokens exposed as CSS custom properties + a Zustand-readable theme object. **No raw hex in components.**

### 3.1 Semantic palette (dark default)

| Token | Dark | Light | Purpose |
|---|---|---|---|
| `--color-bg-base` | `#0B0D10` | `#FAFAFB` | Canvas |
| `--color-bg-raised` | `#151821` | `#FFFFFF` | Cards, panels |
| `--color-bg-sunken` | `#07080B` | `#F3F4F6` | Inset wells |
| `--color-bg-overlay` | `rgba(8,10,14,0.72)` | `rgba(18,20,24,0.56)` | Modals, sheets |
| `--color-ink-strong` | `#F6F7F9` | `#0B0D10` | Primary text, notation |
| `--color-ink-muted` | `#A7ADB8` | `#4A5160` | Secondary text |
| `--color-ink-faint` | `#5D6573` | `#8A93A1` | Hints, timestamps |
| `--color-accent` | `#8BB8FF` | `#2D6CDF` | Interactive primary |
| `--color-accent-contrast` | `#0B0D10` | `#FFFFFF` | Text on accent |
| `--color-success` | `#7DD8A4` | `#1F8A4F` | Correct note, gate pass |
| `--color-warn` | `#F2C85C` | `#946200` | Tempo warning |
| `--color-error` | `#F38A8A` | `#B42B2B` | Wrong note, failure |
| `--color-focus-ring` | `#8BB8FF` | `#2D6CDF` | Focus outline |
| `--color-score-paper` | `#F6F3EA` | `#FFFFFF` | Notation background |
| `--color-score-ink` | `#13161C` | `#0B0D10` | Staff lines, notes |
| `--color-score-hl-correct` | `#65C28C` | `#1F8A4F` | Matched note overlay |
| `--color-score-hl-wrong` | `#E27575` | `#B42B2B` | Missed note overlay |
| `--color-score-cursor` | `#2D6CDF` | `#2D6CDF` | Score-follower cursor |

### 3.2 Contrast guarantees

Every foreground/background pair satisfies **WCAG 2.1 AA** minimum:
- Normal text: contrast ≥ 4.5:1
- Large text (≥ 18.66 px / 14 pt bold): ≥ 3:1
- Non-text UI (icons, focus ring): ≥ 3:1

CI check: `@storybook/addon-a11y` + axe-core on every story.

### 3.3 Never use

- `color: red | green | blue | black | white` — use tokens.
- Opacity to convey state — use a token.
- Color alone to convey meaning (error-red without an icon fails WCAG 1.4.1).

---

## 4. Typography

### 4.1 Type scale

Modular scale, base 16 px, ratio 1.2 (minor third — calm).

| Token | Size (rem) | Line-height | Use |
|---|---|---|---|
| `--text-xs` | 0.75 | 1.4 | Timestamps, meta |
| `--text-sm` | 0.875 | 1.45 | Secondary text |
| `--text-base` | 1.0 | 1.5 | Body |
| `--text-lg` | 1.125 | 1.45 | Emphasised body |
| `--text-xl` | 1.25 | 1.35 | Section label |
| `--text-2xl` | 1.5 | 1.3 | Subheading |
| `--text-3xl` | 1.875 | 1.25 | Page heading |
| `--text-4xl` | 2.25 | 1.2 | Hero |

### 4.2 Font stacks

- **UI Sans** — `"Inter Variable", system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`
- **Monospace** — `"JetBrains Mono Variable", ui-monospace, SFMono-Regular, monospace`
- **Score/Notation** — handled internally by OSMD (Bravura/Emmentaler); **do not override**.

Inter and JetBrains Mono self-hosted under `/fonts/`, preloaded critical weights only (400, 500, 600, 700). No Google Fonts (privacy + CSP simplicity).

### 4.3 Localisation

- `lang="tr"` → `font-feature-settings: "ss01" on` (if needed for diacritics fidelity).
- Line-height +0.05 for TR body text (longer average word length).

---

## 5. Spacing, Radius, Elevation

### 5.1 Spacing scale (4 px grid)

`--space-0` = 0, `--space-1` = 4, `--space-2` = 8, `--space-3` = 12, `--space-4` = 16, `--space-5` = 24, `--space-6` = 32, `--space-7` = 48, `--space-8` = 64, `--space-9` = 96.

Components compose from these. **Never `margin: 13px`.**

### 5.2 Radius

- `--radius-sm` 6 px — inputs, chips
- `--radius-md` 10 px — cards, buttons
- `--radius-lg` 16 px — panels, sheets
- `--radius-pill` 9999 px — segmented controls, tags

### 5.3 Elevation (shadow tokens)

Dark theme uses subtle ring-shadows (not cast shadows) because black-on-black shadows are invisible.

- `--elev-0` — flat (no shadow)
- `--elev-1` — `0 1px 0 rgba(255,255,255,0.04) inset, 0 1px 2px rgba(0,0,0,0.4)`
- `--elev-2` — dropdown, popover
- `--elev-3` — modal, sheet

---

## 6. Motion

### 6.1 Duration tokens

- `--dur-instant` 80 ms — toggle states, hover tint
- `--dur-fast` 140 ms — small dismissals, tooltips
- `--dur-base` 220 ms — modals, sheets, drawers
- `--dur-slow` 360 ms — celebratory/rare only

### 6.2 Easing

- `--ease-standard` `cubic-bezier(.2,.0,.2,1)` — default
- `--ease-emphasized` `cubic-bezier(.3,.0,0,1)` — enter
- `--ease-exit` `cubic-bezier(.4,0,1,1)` — exit

### 6.3 Reduced motion

`@media (prefers-reduced-motion: reduce) { * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; } }` — applied globally; critical cues (score-follower cursor advance) remain but without spring physics.

### 6.4 Notation-timing must beat UI animation

Score-follower cursor advance is **not** a UI animation — it is driven by audio/MIDI timestamps. No token applies. It runs off `AudioContext.currentTime`.

---

## 7. Component Catalogue (Phase 1)

Each component has a Storybook story, a11y test, and visual regression snapshot in CI.

### 7.1 Primitives

- **Button** — variants: `primary`, `secondary`, `ghost`, `danger`; sizes: `sm`, `md`, `lg`; states: default, hover, active, focus-visible, disabled, loading.
- **IconButton** — same states; requires `aria-label`.
- **TextInput** — with label, hint, error, description; supports TR/EN placeholder injection.
- **Checkbox / Switch** — WCAG-sized touch target ≥ 44×44.
- **Segmented Control** — "Guided / Studio" lane toggle; radio semantics.
- **Select** — native `<select>` on mobile; custom listbox on desktop only if native insufficient.
- **Slider** — tempo, volume. `role="slider"`, keyboard arrows, `aria-valuenow`.
- **Dialog / Sheet** — modal dialog (desktop) + bottom sheet (mobile); traps focus, `Escape` dismiss, backdrop opt-out.
- **Toast** — role=status, 4 s default, pause on hover/focus.
- **Tooltip** — desktop-only (no hover on touch); delay 400 ms.

### 7.2 App-specific

- **ScorePane** — OSMD wrapper with lane-mode context.
- **ScoreCursor** — positioned via OSMD internal coords; respects reduced-motion.
- **NoteHighlight** — correct/wrong colouring per-note; survives OSMD reflow.
- **PracticeBar** — play/pause, tempo, loop A/B, hands-separate, blind-play. Sticky bottom on mobile; docked right on desktop ≥ lg.
- **MidiStatusPill** — connected device name + status dot; click opens device picker.
- **LatencyBadge** — current MIDI roundtrip p95; amber if > 30 ms, red if > 50 ms.
- **PieceCard** — cover, title, difficulty, last-practiced; primary CTA "Practice".
- **GateChipsPhase0** — 5 G1 criteria, pass/fail/pending (only rendered under `/sprint0`).
- **ProgressHeatmap** — 90-day practice heatmap; respects prefers-reduced-motion.
- **LicenseEntry** — paste-and-validate field + Stripe Checkout redirect.

### 7.3 Layouts

- **AppShell** — skip-to-content link, header, main, status footer.
- **TwoPaneStudio** — notation on left, practice bar on right (desktop ≥ lg only).
- **StackedMobile** — notation on top, practice bar as sticky bottom sheet.
- **EmptyState** — icon + title + 1-sentence copy + single primary action.
- **ErrorBoundary** — renders `ErrorFallback` with Sentry event ID + "Report" button.

---

## 8. Iconography

- **Lucide** icons — MIT, tree-shakable, consistent stroke.
- Stroke width 1.75, size tokens `--icon-sm` 16, `--icon-md` 20, `--icon-lg` 24.
- Every icon-only button has an `aria-label`.
- Music-specific glyphs (fermata, staccato) rendered by OSMD — we do not re-implement.

---

## 9. Imagery & Illustrations

- **Phase 1 ships zero raster illustrations.** Cost + weight + vibe (no cartoon beginner).
- Avatar placeholders for teacher testimonial cards (Phase 2) are CSS-gradient initials, not stock photos.
- Piece covers (Phase 1) are generated SVG cards with composer name + BWV number + period glyph. One Figma-less tool can emit them from metadata.

---

## 10. Dark / Light Theme

- **Dark default**, with OS-aware auto and explicit override in settings.
- Theme is a CSS class on `<html>`: `.theme-dark` / `.theme-light`.
- Custom properties re-bind per theme class.
- **Score paper never inverts** — dark theme still shows a warm paper-tone score pane (users expect paper-like reading; pitch-black score fatigues the eye).

---

## 11. Accessibility Baseline

See [`ACCESSIBILITY.md`](ACCESSIBILITY.md) for the full WCAG 2.1 AA spec. Baseline rules enforced here:

- Focus-visible outline: **2 px `--color-focus-ring`, 2 px offset**, on every interactive element. Never `outline: none` without a replacement.
- Touch target: **≥ 44 × 44 CSS px** on mobile.
- Text contrast: see §3.2.
- Every form field has an associated `<label>`.
- No `autoplay` for media with sound; piano audio unlocks on first user gesture.

---

## 12. Implementation Notes

- **Tokens live in** `apps/web/src/design/tokens.css` + `tokens.ts` (typed).
- **Components live in** `apps/web/src/ui/` (primitives) and `apps/web/src/features/*/components/` (feature-scoped).
- **Stories live in** `*.stories.tsx` colocated with the component.
- **Visual regression** — Chromatic free tier OR Storybook + Playwright snapshots; decided at Phase-1 Week 4 ADR.
- **No CSS-in-JS runtime** — CSS Modules + design-token CSS variables (bundle-size budget, §4.3).

---

## 13. References

- [`BRAND.md`](BRAND.md) — brand voice, logo, marketing surface
- [`ACCESSIBILITY.md`](ACCESSIBILITY.md) — WCAG 2.1 AA detail
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md) — MIDI, notation, practice UX
- [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md) — breakpoints, touch, PWA
- [`UX-COPY.md`](UX-COPY.md) — voice, errors, empty states
- `MASTER-PLAN.md §1.1` (dual-mode UI), §4.3 (budgets)

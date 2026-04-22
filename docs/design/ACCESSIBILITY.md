# Accessibility — Piano-Practice

> **Target:** **WCAG 2.1 Level AA** across every user-facing screen, every locale (TR + EN Phase 1).
> **Authority:** `MASTER-PLAN.md §5.9` (EAA 2025 compliance), [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md), [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md).
> **Cadence:** Enforced in CI (axe-core + Lighthouse = 100 on a11y). Manual SR pass pre-launch + quarterly.

---

## 1. Conformance Target

- **Standard:** WCAG 2.1 AA (minimum).
- **Also aligned with:** EN 301 549 (EU Accessibility Act 2025).
- **Published statement:** `/legal/accessibility` (TR + EN) from launch.
- **Reporting path:** `accessibility@piano-practice.<tld>`; acknowledged within 5 working days.

---

## 2. Perceivable (WCAG Principle 1)

### 2.1 Text alternatives (1.1)

- Every `<img>` has `alt`. Decorative images: `alt=""` and `role="presentation"`.
- Icons inside buttons/links have either visible label OR `aria-label`. Never icon-only without either.
- Score notation (SVG from OSMD) is inherently graphic — paired with descriptive `<title>` / `<desc>` for SR, plus a textual "Bach Invention No. 1 in C major, BWV 772 — 16 bars" caption on the score pane.

### 2.2 Time-based media (1.2)

- Phase 1 ships no pre-recorded narrated video. Phase-0 prototype demo video (if shown on landing) has captions (TR + EN) and a transcript link.
- Audio-only playback (listen mode, Studio) offers the underlying MIDI notation as its visual equivalent.

### 2.3 Adaptable (1.3)

- Semantic HTML: `<header>`, `<nav>`, `<main>`, `<footer>`, `<article>`, `<section>`.
- One `<h1>` per screen; monotonic heading descent thereafter.
- Form labels via `<label for>` or `aria-labelledby`; `aria-describedby` for hints and errors.
- Reading order matches visual order — tested via `Tab` and SR linear read.
- Content doesn't depend on a specific orientation (5.4 compliance).

### 2.4 Distinguishable (1.4)

- **Color not sole means of conveying info:** wrong note = red + icon + SR announcement.
- **Contrast:** ≥ 4.5:1 for normal text, ≥ 3:1 for large text (≥ 18.66 px or ≥ 14 pt bold) and non-text UI. Verified per token pair in [`DESIGN-SYSTEM.md §3.2`](DESIGN-SYSTEM.md).
- **Resize:** content must remain usable at 200 % zoom without horizontal scroll (except notation, where horizontal scroll is semantic).
- **Images of text:** none (all text is real text).
- **Reflow:** at 320 × 256 CSS px, content scrolls only vertically.
- **Non-text contrast (1.4.11):** focus indicators and component boundaries ≥ 3:1.
- **Text spacing (1.4.12):** no loss of content when users override to `line-height 1.5`, `letter-spacing 0.12em`, `word-spacing 0.16em`, `paragraph-spacing 2em`.
- **Reduced motion (1.4.... see also 2.3):** `prefers-reduced-motion` respected globally.

---

## 3. Operable (Principle 2)

### 3.1 Keyboard accessible (2.1)

- **All functionality keyboard-reachable.** No hover-only controls.
- **No keyboard traps** — `Escape` always closes modals; `Tab` always advances.
- **Shortcuts** (per [`INTERACTION-PATTERNS.md §5`](INTERACTION-PATTERNS.md)) can be remapped or disabled in Settings (2.1.4 compliance).
- **Focus order** logical — tested on every screen.

### 3.2 Enough time (2.2)

- **No timeouts** on practice sessions.
- Stripe Checkout timeout is Stripe-controlled (we don't impose).
- **License validation** is instant; no time pressure.
- **Animations < 5 s** or user-pausable.

### 3.3 Seizures and physical reactions (2.3)

- **No flashing > 3 Hz.** None of our animations flash; reduced-motion media query disables remaining subtle transitions.

### 3.4 Navigable (2.4)

- **Skip link**: first focusable element on every page, jumps to `<main>`.
- **Page titles** (`<title>`) unique and descriptive.
- **Focus-visible** outline: 2 px `--color-focus-ring`, 2 px offset — on every interactive element.
- **Link purpose** (2.4.4) understandable from link text + immediate context.
- **Multiple ways** (2.4.5) — navigation via top bar + footer; keyboard shortcut; library search in Phase 2.
- **Headings and labels** (2.4.6) descriptive.
- **Focus visible** (2.4.7) always.

### 3.5 Input modalities (2.5)

- **Pointer gestures** (2.5.1): no path-based or multipoint-required gestures for essential functionality.
- **Pointer cancellation** (2.5.2): up-event triggers action; we can cancel by dragging off.
- **Label in name** (2.5.3): accessible name starts with the visible label text.
- **Target size** (2.5.5, AAA but we enforce as baseline): ≥ 44 × 44 CSS px on touch.

---

## 4. Understandable (Principle 3)

### 4.1 Readable (3.1)

- **`<html lang>`** always set and updated on locale change.
- **Language of parts** — on mixed passages (e.g., composer names), `lang` attribute noted.
- Reading level targets common-secondary; we do not use jargon without inline explanation.

### 4.2 Predictable (3.2)

- **Focus does not trigger context change** beyond visual focus ring.
- **Input does not auto-submit** without an explicit button or `Enter`.
- **Consistent navigation** across screens (left rail / bottom bar positions, global header).
- **Consistent identification** (same icon = same action across screens).

### 4.3 Input assistance (3.3)

- **Error identification** inline, not a generic banner.
- **Labels / instructions** for every input.
- **Error suggestion** where feasible ("License format looks wrong — paste from your purchase email").
- **Error prevention** on destructive actions (refund cancel, data import overwrite, settings reset) via confirmation.

---

## 5. Robust (Principle 4)

### 5.1 Compatible (4.1)

- **Parsing** (4.1.1, superseded in WCAG 2.2 but we still clean): valid HTML via strict TS templating + CI lint.
- **Name, role, value** (4.1.2): every custom control uses the right ARIA pattern (dialog, tablist, slider, switch).
- **Status messages** (4.1.3): `aria-live="polite"` for non-urgent, `role="alert"` for critical (used sparingly).

---

## 6. Screen Reader Expectations

### 6.1 Tested screen readers

- **NVDA** (Windows, Firefox + Chrome).
- **JAWS** (Windows, Chrome) — nice to pass; not blocking.
- **VoiceOver** (macOS, Safari + Chrome).
- **VoiceOver** (iOS, Safari).
- **TalkBack** (Android, Chrome).

### 6.2 Practice-session SR behaviour

- On mount: announce "Practice session: Bach Invention No. 1 in C major, Guided lane. Press Space to begin."
- On bar boundary: announce "Bar 4 — played 3 of 4 notes correctly" (batched; not per-note).
- On completion: announce "Session complete — 87 % accuracy overall. Summary below."
- On wrong note (if enabled in Settings → Accessibility): announce "Missed" (terse, debounced 2 s).

### 6.3 Non-practice screens

- Route transitions move focus to `<h1>`; SR reads new page heading.
- Dialogs trap focus; `Escape` dismisses; announced with `role="dialog"` + `aria-labelledby`.
- Toasts use `role="status"` for non-critical, `role="alert"` for errors.

---

## 7. Keyboard Reference (overview)

Full list in [`INTERACTION-PATTERNS.md §5`](INTERACTION-PATTERNS.md). Highlights:

- `Space` — play/pause in practice.
- `?` — shortcuts overlay anywhere.
- `Esc` — close dialog / clear loop.
- `Tab` / `Shift+Tab` — focus traversal.
- Arrow keys — grid navigation in Library.

Customisation: Settings → Appearance → Shortcuts. Conflicts detected + user warned.

---

## 8. Component-Specific A11y Contracts

| Component | Pattern | Notes |
|---|---|---|
| Button | `<button>` with `aria-label` if icon-only | disabled state announced |
| Dialog | `role="dialog"`, `aria-modal="true"`, `aria-labelledby` | focus trap; Escape dismiss |
| Tabs | `role="tablist"` + `role="tab"` + `role="tabpanel"` | arrow-key nav |
| Combobox | `role="combobox"` WAI-ARIA 1.2 pattern | Phase 2 only |
| Slider | `role="slider"`, `aria-valuemin/max/now` | arrows adjust |
| Switch | `role="switch"`, `aria-checked` | space toggles |
| Toast | `role="status"` or `role="alert"` | auto-dismiss 4 s; pausable |
| ScorePane | `<figure>` + `<figcaption>` + `aria-describedby` | SR-friendly description of piece |
| ScoreCursor | not announced per-move | bar-boundary live-region carries progress |
| MidiStatusPill | `aria-live="polite"` on change | brief announce "MIDI device connected: Yamaha" |
| LatencyBadge | visual only + `aria-describedby` on hover | not announced continuously |

---

## 9. Localisation Accessibility

- TR and EN both pass WCAG AA.
- Labels never rely on fixed widths — TR strings are often longer; layout accommodates.
- Pluralisation via ICU patterns, not string concatenation.
- Right-to-left: **not supported Phase 1** (no RTL locale in scope); groundwork is correct (logical properties used where possible) so Phase 3 can enable without re-architecture.

---

## 10. Testing & CI

- **axe-core** runs in Playwright E2E on every critical screen — zero violations gate.
- **Lighthouse CI**: accessibility score = **100** (a single violation fails CI).
- **Storybook** `@storybook/addon-a11y` runs on every component story.
- **Manual checklist** run pre-launch:
  - Tab through each screen: focus order logical?
  - NVDA + VoiceOver + TalkBack pass on critical flow?
  - 200 % zoom, horizontal scroll absent?
  - Reduced-motion on: animations calm?
  - Contrast reviewed against `DESIGN-SYSTEM.md §3.2`?
- **Quarterly review** after Phase 1 launch; third-party audit at Phase 2 per `MASTER-PLAN.md §10.2`.

---

## 11. Known Trade-offs

- **Score notation** is inherently graphical; textual alternative is descriptive, not a one-to-one reading of every note. This is accepted industry practice. A dedicated "practice by ear" mode may come in Phase 3.
- **Practice timing feedback** (correct/wrong) is inherently timing-sensitive; SR announcements are batched to avoid flooding.
- **Keyboard shortcuts for practice** overlap with some SR commands; Settings → Accessibility offers a "SR-friendly preset" that disables shortcuts that conflict on each platform.

---

## 12. Accessibility Statement (public)

Published at `/legal/accessibility` in TR + EN. Contents:

- Scope (the web app, excluding third-party Stripe UI).
- Conformance level (WCAG 2.1 AA).
- Known limitations (score notation textual alternative; timing-sensitive feedback).
- Feedback contact (`accessibility@`).
- Last review date (updated quarterly).

Template aligned with EN 301 549.

---

## 13. References

- WCAG 2.1 AA specification
- EN 301 549 (EU Accessibility Act 2025)
- [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md)
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md)
- [`UX-COPY.md`](UX-COPY.md)
- `MASTER-PLAN.md §5.9`, §10.2

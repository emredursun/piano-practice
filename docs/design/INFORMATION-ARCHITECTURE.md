# Information Architecture — Piano-Practice

> **Scope:** Phase 1 site map, navigation model, routing contract. Phase 2/3 deltas inline.
> **Authority:** [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md), ADR-001 (React Router), `MASTER-PLAN.md §12.1`.

---

## 1. Site Map (Phase 1)

```
piano-practice.<tld>
├── /                          Landing (public)
├── /buy/return                Post-Checkout (public, session_id gated)
├── /unlock                    License entry (public)
├── /app                       Library (licensed)
│   ├── /piece/:id             Piece detail
│   ├── /practice/:id          Practice session
│   │   └── ?lane=guided|studio
│   ├── /session/:id/summary   Session summary
│   └── /settings              Settings
│       └── /export            Export / Import
├── /legal
│   ├── /privacy
│   ├── /terms
│   ├── /subprocessors
│   ├── /accessibility
│   └── /security
├── /sprint0                   Phase-0 only (noindex)
│   ├── /landing
│   └── /proto
├── /offline                   SW fallback
├── /404                       Not-found
└── /.well-known/security.txt
```

---

## 2. Navigation Model

### 2.1 Marketing surface (public)

- **Top bar**: logo (→ `/`), pricing anchor, FAQ anchor, "Already bought? Unlock" (→ `/unlock`).
- **Footer**: legal links, locale switcher, subprocessor list.

### 2.2 App surface (licensed)

Desktop (`lg+`):
- **Left rail** (icons + labels): Library, Settings, Progress (Phase 2+).
- **Top strip**: MIDI status pill, latency badge, user menu (license info, sign-out-device).

Mobile (`xs`, `sm`, `md`):
- **Bottom bar**: Library, Practice-resume (if any), Settings (3-tab segmented nav).
- **Top header**: back-button (context-aware), title, overflow menu.

Both surfaces:
- **Skip-to-content** link as first focusable element.
- **Keyboard shortcut overlay** bound to `?`.

### 2.3 Modal / sheet surface

- Bottom sheet on mobile, centered modal on desktop (`≥ md`).
- Triggered by: piece preview from library hover, settings deep-link, refund flow, license-lookup help.

### 2.4 Legal surface

- Reachable from every footer.
- Appears at build-time as statically rendered markdown with a consistent `<article>` + sidebar-TOC shell.

---

## 3. Routing Contract

**Library:** React Router v6.
**Auth guard:** `<Licensed>` HOC; redirects to `/unlock?next=<encodedPath>` on missing/invalid license.
**Scroll restoration:** default per-route; manual preservation on practice-session navigation.

### 3.1 Route params & query params

| Param | Type | Source | Purpose |
|---|---|---|---|
| `:id` (piece) | string, PD-corpus ID (`bwv772`, `bwv773`) | path | Identifies piece |
| `:id` (session) | uuid v4 | path | Identifies practice session |
| `?lane` | `guided` \| `studio` | query | Practice lane mode |
| `?t` | string (signed token) | query on `/unlock` | License token from email |
| `?session_id` | Stripe string | query on `/buy/return` | Stripe session identifier |
| `?next` | urlencoded path | query on `/unlock` | Post-unlock redirect |
| `?locale` | `tr` \| `en` | query | Override session locale (rare) |
| `?theme` | `dark` \| `light` | query | Override session theme (rare) |

### 3.2 Deep-link safety

- Every `?param` is **validated** at the route boundary with Zod (per `CLAUDE.md §5`).
- Invalid params → **route renders with defaults**, never throws.
- `?t` is verified client-side before the URL is cleaned from history (use `history.replaceState`).

### 3.3 Route-level code splitting

| Route | Chunk boundary | Reason |
|---|---|---|
| `/app/practice/:id` | separate chunk, lazy | heaviest (OSMD + Tone.js + WebMIDI) |
| `/app/settings` | separate chunk | rarely loaded on first session |
| `/legal/*` | pre-rendered HTML | not part of SPA shell JS |
| `/sprint0/*` | separate build variant | no accidental production inclusion |

**Initial JS (App shell):** ≤ 200 kb gz (MASTER-PLAN §4.3).

---

## 4. Navigation States

### 4.1 Breadcrumb / back semantics

- **Back** from practice returns to piece detail (not to library) — preserves context.
- **Back** from session summary returns to library (not practice) — emotional closure.
- **Back** from settings returns to whatever screen invoked settings.

### 4.2 Resume affordance

- Last-practised piece floats to top of `/app` as a "Resume" card (if practised within last 14 days).
- Session summary offers "Practice again" (same piece, same lane).

---

## 5. URL-Driven State

State reachable via URL (bookmarkable, shareable):

- Current piece, current lane.
- Current settings tab.
- Locale + theme (via `?locale`, `?theme` — rare).

State **not** in URL:
- Transient play/pause (ephemeral).
- Modal open state (ephemeral).
- MIDI device enumeration (device-local).
- Dexie session buffer (client-local).

---

## 6. Content Taxonomy (Phase 1 corpus)

Corpus organised by:
- **Composer** (Phase 1: Bach only)
- **Work** (2-part inventions, BWV 772–786)
- **Difficulty** (ABRSM grade 5–7)
- **Character tags** (light, contemplative, virtuosic) — filterable in Phase 2
- **Length (bars)** — displayed, not primary filter

Bundled corpus metadata lives at `apps/web/public/corpus/metadata.json` (hash-verified per build; see `MASTER-PLAN.md §7.4`).

---

## 7. Search (Phase 2+)

Phase 1 has 15 pieces — no search UI. A `Ctrl+K` command palette is **Phase 2**.

Phase 2 search indexes: piece title, composer, work number, tags, last-practised age.

---

## 8. Navigation Accessibility

- **Landmark regions** on every screen: `<header>`, `<nav>`, `<main>`, `<footer>` as appropriate.
- **Heading order** enforced: exactly one `<h1>` per screen; descending monotonic thereafter.
- **Focus management** on route change: move focus to `<h1>` of the new screen (announces context to SR).
- **Live regions** reserved for practice-session feedback; not for navigation.
- **Keyboard map** exposed in `?` overlay — same across all authenticated screens.

---

## 9. SEO / Indexing Policy

- **Index:** `/`, `/legal/*`.
- **Noindex:** `/app/*`, `/buy/return`, `/unlock`, `/sprint0/*`, `/offline`, `/404`, `/.well-known/*`.
- `robots.txt` built to align.
- `sitemap.xml` generated at build from the indexable set.
- `og:` and `twitter:` tags for `/` and `/legal/*` only.

---

## 10. Phase-2 Additions (preview)

- `/auth/*` surface introduces a second public shell: sign-in, sign-up, password reset.
- `/app/profile`, `/app/review` added to app left rail.
- `/app/discover` expands the library with filtering.
- Global search (`Ctrl+K`) across pieces, practices, settings.

---

## 11. References

- [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md)
- [`USER-JOURNEY-MAP.md`](USER-JOURNEY-MAP.md)
- [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md)
- `MASTER-PLAN.md §12.1`

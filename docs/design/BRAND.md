# Brand — Piano-Practice

> **Scope:** Brand voice, positioning, logo principles, marketing surface guidelines. Product UI voice is covered in [`UX-COPY.md`](UX-COPY.md).
> **Status:** Phase-1 baseline. Logo work to be completed in Phase-1 Weeks 8–10 alongside reference-recording work.

---

## 1. Positioning

**One sentence:** Piano-practice is the browser-based practice studio for serious pianists — real notation, real sound, real MIDI, with pedagogical honesty that respects your time.

**For whom:**
- Adults 16+ returning to or persisting with the piano.
- Self-learners who have already made the decision to practise; they need a better practice environment, not motivation theater.
- Pre-professional and amateur pianists looking for a credible browser-based companion that isn't a toy.

**Against whom (competitive frame):**
- **Not** a gamified beginner app (Simply Piano, Skoove — respected but different audience).
- **Not** a full DAW (Logic, Pro Tools — wrong tool).
- **Not** a streaming lesson platform (Tonebase, Pianote — passive consumption).
- **Close neighbours:** Henle Library, forScore, MusicXML-browsing tools — but we add score-following, AI fingering, and practice analytics.

**Proof points (v1.0):**
- 15 Bach Two-Part Inventions, engraved from confirmed-PD sources.
- AI fingering suggestions trained on Parncutt 1997 + Czerny 1840 + Busoni 1894 — all PD, explainable, overridable.
- Score-follower with 50 ms chord aggregation (pedagogically accurate timing).
- Real Salamander Grand V3 samples via AudioWorklet.
- Works offline after first load.

---

## 2. Voice

See [`UX-COPY.md §1`](UX-COPY.md) for the canonical voice definition.

Summary:

- **Respectful.** We are talking to an adult who has decided to practise.
- **Specific.** Replace praise with numbers; replace warnings with actions.
- **Calm.** No confetti, no emoji stream, no hype language.
- **Honest about limits.** "Only in Türkiye and EU. Minimum age 16." is a feature of our voice, not a weakness.

Voice exemplars (**do** / **don't**):

| Do | Don't |
|---|---|
| You played 87 % of notes correctly across 16 bars. | Amazing performance!! 🎉🎉 |
| This license has been revoked. If that's a mistake, contact support. | Oops! Something went wrong. |
| Bluetooth MIDI adds latency — wired USB is recommended. | Tip: use a wire! 😊 |
| Minimum age 16. | Perfect for the whole family! |

---

## 3. Naming

- **Product name:** piano-practice (lowercase, hyphenated — matches the repo).
- **Not allowed:** Piano Practice™, PianoPractice, piano-Practice.
- **Marketing variants:** "piano-practice studio" (casual), "the piano-practice browser studio" (technical).
- **Legal entity name:** TBD at WS1-T01 ambiguity resolution (personal vs TR şahıs şirketi vs EU Ltd).

---

## 4. Logo (to be designed)

### 4.1 Principles (for when we brief or draw it)

- **Scores, not screens.** The logo alludes to notation, not screens or apps.
- **Mark before wordmark.** A small mark that survives at 16 × 16 favicon size is primary.
- **One colour first.** Must work in monochrome (print, fax-quality, single-colour browser tab).
- **No musical cliches.** No whole-note head as "o", no treble clef as "d".
- **Honest shapes.** Geometric, honest line-weights. No shadows, no gradients.
- **Aligns with Inter / JetBrains Mono.** Geometric sans cousin in the mark.

### 4.2 Placeholder

Until the final mark is produced, the wordmark is simply:

```
piano-practice
```

in Inter SemiBold (600), `--text-xl`, `--color-ink-strong`, letter-spacing −0.01em.

### 4.3 Do-not guidelines

- No rotation.
- No warping, shearing, or outline effects.
- Never on low-contrast backgrounds.
- Clear space on all sides = height of the "p".
- Minimum size: 96 px wide for wordmark; 16 px for icon.

---

## 5. Colours (brand layer)

The product design-system in [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md) defines the product surface. For marketing:

| Role | Value | Use |
|---|---|---|
| Brand primary | `#2D6CDF` | Buttons, links, accents (matches `--color-accent` light) |
| Brand ink | `#0B0D10` | Logo on light, type |
| Brand paper | `#F6F3EA` | Score-paper warm tone — also the off-white we prefer over pure `#FFFFFF` on marketing surface |
| Brand dark | `#0B0D10` | Dark-theme canvas |
| Brand muted | `#4A5160` | Secondary type |

---

## 6. Typography (brand layer)

- **Primary:** Inter Variable.
- **Monospace accent (technical contexts):** JetBrains Mono Variable.
- **Display size on marketing:** `--text-4xl` on desktop; tighter letter-spacing (−0.02em).
- Never use system UI fonts for marketing surface.

---

## 7. Imagery

- **Photography:** none in Phase 1. No stock pianists. No shutterstock learners.
- **Illustration:** none in Phase 1 (see [`DESIGN-SYSTEM.md §9`](DESIGN-SYSTEM.md)).
- **Score images:** real OSMD-rendered screenshots of Bach Inventions in SVG. These are our visuals.
- **Video:** a single 30-second demo on the landing page, recorded by the founder on the prototype. No voiceover in v1.0.

---

## 8. Tone for Specific Channels

| Channel | Register | Example |
|---|---|---|
| Landing page | Confident, sober | "Real engraved notation. Real sampled Steinway. Real MIDI." |
| Newsletter (Phase 1 "Practice Notebook") | Reflective, first-person | "This week I worked on bar 12 of BWV 774. Here is what I noticed." |
| Reddit / forums | Substantive, no pitch | Share a fingering reasoning breakdown, not a link dump. |
| YouTube (Phase 2+) | Pedagogical | Walk through a practice session, not a product feature tour. |
| Support email | Warm, direct | "Your license has been revoked because of the refund on {date}. Here is what happens next." |

---

## 9. Competitive Messaging Rules

- **Never diss a competitor by name.** Stating our positioning is enough.
- **Never claim "best" / "only" / "unique".** Say what we do and let the reader judge.
- **Never over-promise pedagogy.** We are a practice studio; we do not replace a teacher.

---

## 10. Legal Brand Notices

- Composer names (Bach, Czerny, Busoni, etc.) are used as historical factual references — no implied endorsement.
- Third-party brand names (Yamaha, Kawai for MIDI devices) appear only in helpful troubleshooting copy, without claim of partnership.
- Salamander Grand V3 attribution published per CC-BY 3.0 in `/legal/subprocessors`.

---

## 11. Phase Evolution

- **Phase 1:** placeholder wordmark, no photography, minimal illustration.
- **Phase 2:** final mark delivered; newsletter template finalised; style-guide (this doc) reviewed.
- **Phase 3:** expanded brand system for teacher tooling + subscription tier; tone adapted per audience.

---

## 12. References

- [`UX-COPY.md`](UX-COPY.md) — product voice
- [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md) — product surface tokens
- `MASTER-PLAN.md §8.2` (Phase-1 owned channel build)

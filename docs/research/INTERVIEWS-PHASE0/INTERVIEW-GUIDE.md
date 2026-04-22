# Interview Guide — Phase 0 Customer Discovery

> **Format:** Semi-structured. **30 minutes** + 5 min admin. JTBD + willingness-to-pay anchoring.
> **Authority:** `MASTER-PLAN.md §2.3 WS2`; `/plan` output WS2-T01.
> **Segments:** piano teachers (TR + DE), adult self-learners (TR + EU), advanced amateurs / pre-professionals.

---

## 1. Pre-Interview Checklist (2 min before call)

- [ ] Consent form signed / replied to.
- [ ] Pseudonym assigned (`P01`, `P02`, …), mapping stored **only** in `RAW/MAPPING.txt` (gitignored, encrypted).
- [ ] Recording software ready (QuickTime / OBS), test run passed.
- [ ] This guide open; notes template at `RAW/<pseudonym>/notes.md`.
- [ ] Segment-specific prompt set bookmarked (§5).

---

## 2. Opening (2 min)

Read verbatim:

> "Thanks for making time. As a reminder, this call is research, not a sales pitch. I'm recording audio and video with your consent; you can stop at any time. I'll ask about how you currently practise, what's frustrating, and what you'd hope for. There are no wrong answers. The €25/40 Amazon code goes out within 2 business days regardless of what you say."

Pause for "ok" / nod. Start recording explicitly: "I'm starting the recording now."

---

## 3. Warm-Up (3 min)

1. "How long have you been playing piano?"
2. "What does your typical practice week look like in terms of days and total hours?"
3. "Do you have a current teacher, or are you mostly self-learning?"

Purpose: relaxes participant, calibrates segment fit, gives you context to reference later.

---

## 4. Core — JTBD Block (18 min)

The aim is to understand **what job the participant hires a practice tool to do**. Avoid pitching; avoid leading questions.

### 4.1 Context of their current practice (5 min)

- "Walk me through the last time you sat down to practise. What did you actually do — bar by bar if you can?"
- "What were you trying to get better at that day?"
- "When you finished, how did you know whether it went well?"

### 4.2 Friction and workarounds (5 min)

- "What's the most frustrating part of your current practice setup?"
- "What do you do when you hit something you can't play? Walk me through it."
- "Have you tried any app, website, or tool for practice?" *(listen for names — competitive intel)*
- "If you did, what made you stop using it — or keep using it?"

### 4.3 Wishes and workarounds (4 min)

- "If a practice tool could do *one* thing better than what you have now, what would it be?"
- "Have you ever paid for something related to practice — app, course, book, lesson pack, gear? What made that feel worth it?"

### 4.4 Willingness-to-pay anchor (4 min)

Phrase carefully to avoid leading:

- "Imagine a browser-based tool — no install, no account — that renders real engraved notation, plays sampled Steinway audio, listens to your MIDI, shows you which note you missed, and suggests fingerings based on public-domain editions. Not a streaming-lesson service. A practice companion. Does that sound like something you'd try?"

If yes:

- "What would make you worth $29 one-time, lifetime access, no subscription?" *(listen for: specific features, trust signals, comparisons)*
- "What would make you walk away at $29?" *(listen for: must-have objections, credibility gaps)*

If no:

- "What would you expect such a thing to cost, if it was worth it?"
- "What would make you change your mind — what's missing from my description?"

Do **not** mention Bach-only corpus unless they ask for scope. If they ask:

- "v1.0 is 15 Bach Two-Part Inventions. Broader repertoire ships later if enough people want it."

**Capture verbatim any number they say.** Dollar value + their justification. These are the raw signals for G1-C2 triangulation.

---

## 5. Segment-Specific Probes (choose one set, 3 min)

### 5.1 Piano teachers

- "Do you currently recommend any digital tools to your students? Why those?"
- "What would stop you from recommending a tool like this to a 16-year-old student of yours?"
- "If it helped your students, would you expect them or you to pay for it?"

### 5.2 Adult self-learners

- "What's your relationship with digital piano vs. acoustic? MIDI setup at home?"
- "Do you prefer to learn by watching, reading, listening, or something else?"
- "What keeps you coming back to practice week after week — or what makes you stop?"

### 5.3 Advanced amateurs / pre-professionals

- "How do you currently maintain pieces you've already learned?"
- "Does 'spaced repetition for repertoire' sound like a useful idea, a gimmick, or something else?"
- "Would you trust an AI fingering suggestion? What would have to be true for you to override your teacher's fingering based on it?"

---

## 6. Close (2 min)

- "Anything I should have asked but didn't?"
- "Would you be OK if I followed up in a few weeks with a quick update when we launch?"
- "Anyone you'd recommend I also talk to?" *(snowball sampling)*
- "Recording is stopping now. €25/€40 Amazon code goes to <email confirmed> within 2 business days."

Stop recording. Verbally thank. Hang up.

---

## 7. Post-Interview (within 30 minutes)

- [ ] Save recording file to `RAW/<pseudonym>/recording.mov` (gitignored).
- [ ] Update notes at `RAW/<pseudonym>/notes.md` while memory is fresh:
  - Key quotes (verbatim, with timestamp).
  - JTBD job statement (1 sentence).
  - Willingness-to-pay: stated number + justification + perceived price-ceiling.
  - Surprises / outliers.
  - Red flags (if any) — e.g., participant seemed coached, distracted, off-segment.
- [ ] Send €25/€40 Amazon code (no delay → integrity of unconditional pay).
- [ ] Log in `OUTREACH-LOG.md` — status = DONE, notes path.

---

## 8. Synthesis Rubric (for the final sweep at WS2-T06)

Each interview binary-tagged on:

- **Pain validation** — Y/N: participant expressed a real, specific friction (not generic).
- **Willingness-to-pay validation** — Y/N: stated willingness at or above $29 OR articulated a higher price they'd accept.
- **JTBD alignment** — Y/N: the job-to-be-done aligns with our value proposition.

**G1-C3 passes** if ≥ 10/15 interviews satisfy **all three** Y tags.

Capture:
- Surprising features requested (outside our Phase-1 scope).
- Competitor mentions (product names + user sentiment).
- Segment-specific friction patterns.

---

## 9. Bias Mitigation Checklist

- [ ] Avoided leading questions ("wouldn't you agree…").
- [ ] Avoided social-desirability framing ("most serious pianists would say…").
- [ ] Asked for *behaviour*, not *opinion* ("walk me through the last time" beats "what do you think about").
- [ ] Captured participant's own numbers before mentioning $29.
- [ ] Recorded outliers and contradictions — they are signal, not noise.

---

## 10. Do-Not-Ask List

Never ask:
- Health, medical, or disability information.
- Financial details beyond "what have you paid for" (no salary, no net worth).
- Political, religious, philosophical, or sexual-orientation.
- Other special-category data (KVKK Madde 6 / GDPR Art. 9).

If a participant volunteers any of the above, do **not** record it in notes; redirect gently.

---

## 11. References

- [`CONSENT-FORM.md`](CONSENT-FORM.md)
- [`OUTREACH-TEMPLATES.md`](OUTREACH-TEMPLATES.md)
- `../../decisions/PHASE0-SETUP.md §5`
- `MASTER-PLAN.md §2.3` (WS2 description)
- JTBD reference: Christensen + Ulwick

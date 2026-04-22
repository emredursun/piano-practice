# Content QA Pipeline — Piano-Practice

> **Scope:** Every piece added to the corpus passes this pipeline.
> **Authority:** `MASTER-PLAN.md §7.4`, ADR-011.

---

## 1. Pipeline Stages

For every piece `{piece_id}`:

1. **Source verification.**
   - PD status per jurisdiction (TR, EU, UK, US).
   - Edition provenance (year, editor, publisher).
   - Recorded in `docs/legal/REPERTOIRE-PROVENANCE.md`.

2. **MusicXML engraving.**
   - Engraved in MuseScore 4 from the PD source.
   - Exported as MusicXML 4.0 (compressed `.mxl` supported but uncompressed `.musicxml` preferred for diff-ability).
   - Stored at `corpus/musicxml/{piece_id}.musicxml`.

3. **Automated lint.**
   - Validates against MusicXML 4.0 XSD.
   - Notation correctness: range checks (no out-of-88-key notes for piano), no missing key/time signatures, voice continuity.
   - Fingering plausibility: pianoplayer runs over the piece and emits warnings if impossible hand-spans are inferred.

4. **Manual review.**
   - Phase 1: founder reviews against the PD source.
   - Phase 2+: one qualified pianist contractor.
   - Sign-off recorded in `corpus/reviews/{piece_id}.md` with reviewer initials + date.

5. **Reference recording.**
   - MIDI reference (from MuseScore export) + audio reference (founder's own performance).
   - Audio released CC-BY-NC (see `MASTER-PLAN.md §7.1`).
   - Files at `corpus/reference/{piece_id}.mid` + `.mp3`.

6. **Difficulty rating.**
   - ABRSM / RCM equivalent grade documented.
   - Fingering complexity score (average spans + voice crossings) computed by pianoplayer.
   - Written into `corpus/metadata.json` row.

7. **Metadata commit.**
   - `corpus/metadata.json` entry:
     ```json
     {
       "id": "bwv772",
       "composer": "Bach, J.S.",
       "workNo": "BWV 772",
       "title": "Invention No. 1 in C major",
       "grade": 5,
       "bars": 22,
       "lengthSec": 86,
       "edition": { "year": 1853, "publisher": "Bach-Gesellschaft" },
       "pdJurisdictions": ["TR","EU","UK","US"],
       "fingeringSources": ["czerny-1840","busoni-1894","pianoplayer"],
       "hash": "sha256:<hash-of-files>"
     }
     ```
   - `hash` computed from MusicXML + fingering manifests + reference MIDI; CI recomputes and fails if mismatch.

8. **CI verification.**
   - On every build, the corpus hash is verified — corpus tampering is immediately surfaced.

---

## 2. Roles

- **Founder (Phase 1):** source verification, engraving, automated lint, manual review, reference recording, ratings, commit.
- **Contractor (Phase 2+):** manual review + ratings; founder approves final.
- **Legal counsel:** annual PD status review.

---

## 3. Tooling

| Tool | Role |
|---|---|
| MuseScore 4 | Engraving / MusicXML export |
| `xmllint` / MusicXML 4.0 XSD | Schema validation |
| pianoplayer | Fingering + plausibility lint |
| Custom script `scripts/corpus-audit.mjs` | Hash + metadata validation |
| FFmpeg | Reference recording encode |

---

## 4. Exceptions

- **User-authored pieces** (exercises like "C major scale"): skip steps 1 and 5; add to `provenance.md` with "hand-authored by founder" note.
- **Imported openly-licensed engravings** (CPDL/IMSLP): verify the engraver's licence permits redistribution; record in provenance.

---

## 5. Phase-2 Corpus Expansion

Same pipeline; increased volume (30+ pieces). Expect ~4 h per piece end-to-end. Batched in sprints.

---

## 6. References

- `MASTER-PLAN.md §7.1`, §7.4, §5.10
- ADR-011
- [`../legal/REPERTOIRE-PROVENANCE.md`](../legal/REPERTOIRE-PROVENANCE.md)

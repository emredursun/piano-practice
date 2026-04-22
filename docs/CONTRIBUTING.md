# Contributing — Piano-Practice

> **Audience:** Solo founder + any AI agent operating in this repo (and future collaborators).
> **Authority:** [MASTER-PLAN.md](MASTER-PLAN.md) — all process rules below derive from §4 (CI/CD), §5 (Security), §11 (Gates), §12 (Governance).

Even as a solo project, we run with team-grade process. Reason: AI agents are the primary collaborators, and they need the same predictable rails a human team would need.

---

## 1. Before You Start Work

1. **Read `CLAUDE.md`** at the repo root. Especially §8 ("Hard NO List").
2. **Know which gate/phase you serve.** G1, G2, G3, or a named Phase-0 workstream. Work that doesn't map to one is out of scope by default.
3. **Check for an existing ADR.** If the change touches an architectural decision, amend or supersede the ADR in the same PR — do not silently diverge.
4. **If you're an AI agent:** use the `planner` agent first for any multi-file change; use `tdd-guide` for any new module.

---

## 2. Branching

```
main                  ← protected; only PR merges, green CI required
  └── feat/<scope>    ← new features
  └── fix/<scope>     ← bug fixes
  └── refactor/<scope>
  └── docs/<scope>
  └── chore/<scope>
  └── perf/<scope>
  └── ci/<scope>
```

Scope is a short kebab-case noun: `score-follower`, `stripe-webhook`, `adr-005`, `phase-0-prototype`.

**Never** commit directly to `main`. **Never** force-push a branch someone else (or an agent) is reviewing.

---

## 3. Commits

Format:

```
<type>: <subject, imperative, ≤ 72 chars>

<optional body — why, not what>
<optional footer — refs, breaking change>
```

Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `ci`.

Rules:
- No co-author attribution (globally disabled in `~/.claude/settings.json`).
- No `console.log`, no commented-out code, no "WIP" commits on `main`.
- Signed commits required on `main` (Phase 2+ when collaborators exist; recommended solo).

Examples:

```
feat(score-follower): add 50 ms chord aggregation window

Closes MASTER-PLAN §2.3 workstream 4 (technical feasibility prototype).
Ref ADR-005.
```

```
docs(adr): supersede ADR-013 — device-local only in v1.0
```

---

## 4. Pull Requests

Every PR must include:

- **Linked gate/phase** (e.g., "Phase 1 — license validator" or "Pre-G1 prototype").
- **Risk level**: low / medium / high. Security-touching PRs are never "low".
- **Test plan**: bulleted checklist of what was tested and how.
- **Rollback plan**: how to revert if this breaks production (e.g., "Cloudflare Pages rollback to previous deploy").
- **ADR impact**: which ADRs this touches, amends, or supersedes. "None" is a valid answer.

PRs without these sections do not merge.

### PR Size

- **≤ 400 lines net change** preferred; ≤ 800 hard cap (excluding lockfiles, generated files, snapshots).
- Larger than that → split it. Large PRs are a review bottleneck and hide defects.

### Review

- **Solo (Phase 1):** self-review by walking through the diff in Zed before merging. The `git push` hook triggers this automatically.
- **With collaborators (Phase 2+):** one approving review required on `main`; security-touching files require a second review.
- **AI agents review too.** Run the `code-reviewer` agent on every non-trivial diff. Run `security-reviewer` on any diff touching auth, webhook, license, CSP, upload pipeline, or secrets.

---

## 5. CI Gates (`.github/workflows/ci.yml`)

From `MASTER-PLAN.md §4.2`. A PR cannot merge until all of:

- TypeScript strict typecheck passes
- ESLint zero errors, ≤ 10 warnings
- Vitest passes, **≥ 80 % line coverage on critical paths** (score-follower, audio engine, MIDI handler, license validator)
- Playwright E2E passes (auth-less critical paths)
- Lighthouse CI: Performance ≥ 90, Accessibility = 100, Best Practices ≥ 95, PWA pass
- Bundle size within budget (see `MASTER-PLAN.md §4.3`)
- `npm audit --audit-level=high` clean
- `gitleaks` secret scan clean
- License allowlist enforced (MIT, BSD, Apache-2.0, ISC, CC-BY, CC0)
- CycloneDX SBOM generated (attached to release)

Do **not** disable a CI check to "unblock" a PR. Fix the root cause or open an ADR to change the check.

---

## 6. Testing

### 6.1 TDD Is the Default

Write the failing test first. See `CLAUDE.md §6`.

### 6.2 Coverage

- **Critical paths: ≥ 80 %** line coverage. Non-negotiable.
- **Overall: target ≥ 70 %**, but don't chase a number on trivial glue code.
- Coverage measured in CI via `vitest --coverage`; HTML report uploaded as artifact.

### 6.3 Test Levels

| Level | Tool | Scope |
|---|---|---|
| Unit | Vitest | Pure functions, reducers, validators |
| Component | Vitest + Testing Library | React components in isolation |
| Integration | Vitest + msw, Workers local | Webhook handler, DB migrations |
| E2E | Playwright | Critical user flows (load → play → complete) |
| Accessibility | axe-core in Playwright | Zero violations on critical screens |
| Performance | Lighthouse CI | Budget enforcement |

### 6.4 Fixtures & Test Data

- Stripe: use Stripe test keys + test webhook signing secret. Never test-key in code; use `STRIPE_TEST_*` env vars.
- MIDI: synthetic MIDI streams in `test/fixtures/midi/` — do not require a physical device for unit tests.
- MusicXML: a single known-good piece (BWV 772 excerpt) in `test/fixtures/musicxml/` — confirmed PD.

---

## 7. Security Process

See [SECURITY.md](SECURITY.md) and `MASTER-PLAN.md §5`.

Before any commit touching auth, webhook, license, CSP, upload pipeline, or secrets:

1. Run the `security-reviewer` agent on the diff.
2. Verify no secret was added (gitleaks local hook should catch this — if you bypassed it, run it manually).
3. If the change weakens a boundary, file an ADR **before** merging.

Responsible disclosure: see `/.well-known/security.txt` (published at launch). Coordinated disclosure in 90 days; reports to `security@piano-practice.<tld>`.

---

## 8. Documentation

- **ADR for every decision** that would confuse a future reader if not written down. "Why Vite and not Next.js" is an ADR. "Why I named this variable X" is not.
- **Keep the Master Plan pristine.** It is version-controlled but rarely amended — amendments require the same quality-gate rigor as the original. All day-to-day decisions happen in ADRs.
- **Do not create new `.md` files in root** beyond `README.md` and `CLAUDE.md`. Everything else lives under `docs/`.
- **Cross-reference.** Every doc should link back to the Master Plan section it derives from.

---

## 9. Dependencies

- **Prefer battle-tested libraries** over hand-rolled solutions in the audio/notation/MIDI domain. That's exactly why we chose OSMD, Tone.js, WebMidi.js.
- **Renovate bot** opens PRs for patch updates — auto-merge after CI green.
- **Minor/major updates** require manual review; run changelog review in the PR description.
- **`npm install --ignore-scripts`** is the policy — defeats malicious postinstall scripts.
- **License allowlist:** MIT, BSD, Apache-2.0, ISC, CC-BY, CC0. Anything else needs an ADR.

---

## 10. Operations

- **On-call (Phase 1):** solo founder is sole pager. SEV-1 response 15 min, SEV-2 within 1 h, SEV-3 within 24 h. See `docs/operations/IR-PLAN.md`.
- **Tabletop exercise:** Week 14 before Phase-1 launch; quarterly from Phase 2.
- **Incident records:** every SEV-1 and SEV-2 gets a write-up in `docs/operations/incidents/YYYY-MM-DD-<slug>.md`.

---

## 11. If You're Stuck

- **Blocker, not a bug:** open an issue / note in `docs/decisions/` and flag it at the next gate review.
- **Bug you can't isolate:** write a failing test that reproduces it first, then debug.
- **Ambiguity in the Master Plan:** **do not guess**. Flag it, ask, and amend the Master Plan or add an ADR.

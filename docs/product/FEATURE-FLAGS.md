# Feature Flags — Piano-Practice

> **Authority:** ADR-016 (PostHog free tier), `MASTER-PLAN.md §4.2`.
> **Scope:** Every user-facing change is behind a flag with an explicit rollout plan.

---

## 1. Why Flags

- **Kill-switch** for a bad release without redeploy.
- **Rollout percentage** for progressive releases.
- **A/B experimentation** for landing / pricing tests (post-Phase-0).
- **Conditional feature gating** by phase, by geo, by device capability.

Non-negotiable per `MASTER-PLAN.md §4.2`: every user-facing change ships behind a flag.

---

## 2. Catalogue (Phase 1)

Each flag has a **key**, **default**, **owner**, **scope**, **sunset**.

### 2.1 Marketing / Landing

| Key | Default | Scope | Sunset |
|---|---|---|---|
| `landing.heroVariant` | `A` | `/` | After first post-launch A/B decision |
| `landing.demoVideo` | `true` | `/` | When Phase-2 testimonials replace |
| `landing.priceDisplayCurrency` | `auto` (cf-ipcountry) | `/` | Permanent |

### 2.2 Onboarding / Install

| Key | Default | Scope | Sunset |
|---|---|---|---|
| `install.promptOnSecondVisit` | `true` | `/app` | Permanent |
| `install.iosGuideEnabled` | `true` | iOS Safari | When iOS gets programmatic prompt |
| `onboarding.firstSessionHints` | `true` | `/app/practice/:id` | Permanent |

### 2.3 Practice Session

| Key | Default | Scope | Sunset |
|---|---|---|---|
| `practice.lane.defaultGuided` | `true` | `/app/practice/:id` | Phase 2 re-eval |
| `practice.fingering.default` | `ai` | `/app/practice/:id` | If user-testing says `editorial` wins |
| `practice.wrongNote.sranAnnounce` | `false` | `/app/practice/:id` | Enabled per user in Settings |
| `practice.studio.enabled` | `true` | `/app/practice/:id?lane=studio` | Permanent |
| `practice.blindPlay.enabled` | `true` | Studio | Permanent |
| `practice.tempoRamp.maxRatio` | `1.2` | Studio | Permanent |

### 2.4 Audio / MIDI

| Key | Default | Scope | Sunset |
|---|---|---|---|
| `audio.sampleSet.default` | `lite` | global | When full set becomes default |
| `audio.contextLatencyHint` | `interactive` | global | Platform-conditional |
| `midi.sysex` | `false` | global | Permanent (security) |

### 2.5 Data / Privacy

| Key | Default | Scope | Sunset |
|---|---|---|---|
| `rum.enabled.byDefault` | `false` | global | Permanent (opt-in only) |
| `sentry.enabled` | `true` | global | Permanent |
| `honeycomb.sampling.rate` | `0.25` | global | Tuned over time |
| `export.includeFingeringOverrides` | `true` | `/app/settings/export` | Permanent |

### 2.6 Kill-switches (Phase 1)

| Key | Default | Trip condition |
|---|---|---|
| `killswitch.stripeCheckout` | `false` | Set to `true` during a Stripe incident to hide Buy CTA + show banner |
| `killswitch.midiInput` | `false` | Set to `true` if a browser/OS bug breaks MIDI globally |
| `killswitch.audioWorklet` | `false` | Fallback to ScriptProcessorNode if AudioWorklet regresses |

### 2.7 Phase-0 only (disposable)

| Key | Default | Scope | Sunset |
|---|---|---|---|
| `sprint0.enabled` | `true` during Phase 0, `false` after | `/sprint0/*` | Deleted post-G1 |
| `sprint0.ads.trackingEnabled` | `true` during run | `/sprint0/landing` | Deleted post-G1 |

---

## 3. Conventions

- **Naming:** `scope.subscope.property` in lowerCamelCase.
- **Values:** boolean first-class; string/number/object only when truly needed.
- **Documentation:** every flag has a PostHog description and a pointer to this file.
- **Testing:** every feature test runs once with the flag on and once off (or representative variants).
- **CI:** flags referenced in code but missing from this catalogue fail the build (lint rule).
- **No long-lived experimentation flags.** If a flag is still around 3 months post-launch without a sunset date, file an issue.

---

## 4. Rollout Patterns

### 4.1 Internal canary (founder only)

Target: logged-in identity (Phase 2+) or Cloudflare Worker cookie. Phase 1 uses URL signal (`?_ff=key:value`) — not secret, just convenience.

### 4.2 Percentage rollout

PostHog `rollout: N%` with deterministic hashing over `distinct_id`. Start at 10 %, double every 24 h if SLOs hold.

### 4.3 Conditional rollout

Gate by:
- Geo (`TR`, `EU`).
- Device capability (e.g., `navigator.userAgent` for iOS Safari-specific flags).
- Phase (0 / 1 / 2 / 3).

---

## 5. Kill-Switch Drill (quarterly)

- Trip `killswitch.stripeCheckout`.
- Observe banner appears and CTA hides.
- Confirm no user data lost or corrupted.
- Return flag to `false`.
- Record in `docs/operations/IR-PLAN.md` tabletop log.

---

## 6. Evaluation at Boundaries

Flags are evaluated at:

- **Server (Worker):** for webhook + license endpoints.
- **Client init:** on app boot — cached for the session.
- **Per-render:** only for flags that may change mid-session (kill-switches).

Never evaluate flags inside hot audio/MIDI paths.

---

## 7. Observability

- Every flag evaluation emits a PostHog event with `flag_key`, `variant`, `distinct_id` (anonymous).
- Sentry tags include current flag state for every captured exception.

---

## 8. Deprecation Policy

When a flag is sunset:

1. PR removes the flag reference in code.
2. PR removes the row from this catalogue.
3. PostHog entry archived (not deleted — preserves historical event context).

---

## 9. References

- `MASTER-PLAN.md §4.2` (CI/CD — feature flags mandatory)
- ADR-016
- PostHog docs

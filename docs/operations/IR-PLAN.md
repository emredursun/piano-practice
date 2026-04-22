# Incident Response Plan — Piano-Practice

> **Authority:** `MASTER-PLAN.md §4.5` (on-call & IR), §5.1 (threat model), §10.2 (compliance).
> **Solo operator:** Phase 1 has one on-call (founder). Phase 2+ expands.

---

## 1. Severity Matrix

| Level | Definition | Examples | Ack time | Target fix |
|---|---|---|---|---|
| **SEV-1** | Data breach; payment failure ≥ 1 h; total outage; revocation flow broken | Stripe webhook compromised; license key leakage; `/app` hard-down | **15 min** | ASAP, ≤ 4 h |
| **SEV-2** | Degraded performance or partial feature outage | Audio latency > 100 ms across users; MIDI broken on Chrome; CSP block of core resource | **1 h** | ≤ 24 h |
| **SEV-3** | Cosmetic or individual-user | A single user can't unlock; one locale string missing | **24 h** | Next release |
| **SEV-4** | Enhancement | Performance nice-to-have; wording tweak | — | Backlog |

---

## 2. Pager Flow (Phase 1, solo)

1. **Sentry alert** → SMS to founder (2 %/1 h or 5 %/6 h burn).
2. **Status page banner** (manual) within **1 h** for SEV-1.
3. **Stripe webhook failures** → email alert within 5 min.
4. **User-reported** via `support@` → triaged within 24 h.

Backup: if founder unreachable for > 4 h, the **designated technical executor** (named in `docs/operations/EXECUTOR.md`, sealed envelope) receives credentials to **pause ads / take down product** only — never to make code changes.

---

## 3. Incident Runbook (template)

For every SEV-1 / SEV-2 open `docs/operations/incidents/YYYY-MM-DD-<slug>.md`:

```markdown
# Incident: <slug>

- Severity:
- Detected at:
- Acknowledged at:
- Resolved at:
- Duration:
- User impact (number + geography):

## Timeline
HH:MM — <event>
...

## Root cause
<5 Whys>

## Resolution
<what fixed it>

## Follow-up actions
- [ ] Code fix
- [ ] Monitoring gap closed
- [ ] Doc updated
- [ ] Tabletop scenario added

## Lessons learned
<what we do differently>
```

No blame, no heroes — structured learning.

---

## 4. GDPR Art. 33 / KVKK Art. 12 Breach

- **72 h notification** window to DPA for any personal-data breach.
- Templates pre-drafted in `docs/security/IR-TEMPLATES/` (TR + EN), authored Week 4.
- Contact: `privacy@piano-practice.<tld>` + legal counsel.
- Data-subject notification if risk to rights and freedoms is material.

---

## 5. Stripe / Payments Incident

### 5.1 Webhook failure surge

- Symptom: Stripe dashboard shows retries / failures.
- Action: temporarily trip `killswitch.stripeCheckout` → banner shows "Buying temporarily paused".
- Diagnose: Worker logs (Cloudflare dashboard) → correlate with `stripe.webhooks.constructEvent` errors.
- Recover: redeploy Worker; re-enable checkout; backfill missed webhooks via Stripe dashboard "Resend".

### 5.2 Unauthorised license issuance

- Symptom: revocation list grows with events not tied to real customers.
- Action: **rotate Ed25519 signing key** → all existing licenses invalidated (nuclear).
- Communicate: email all customers with the new license via Stripe customer list.
- Diagnose: Worker audit log; Sentry breadcrumbs.
- Post-mortem required.

---

## 6. Audio / MIDI Global Regression

- Symptom: RUM shows sustained spike in `audio.latency.p99` or MIDI input drops.
- Check: browser release notes; Web Audio / Web MIDI spec changes.
- Action: trip `killswitch.audioWorklet` if needed; show toast "Audio engine updated; if you notice issues, refresh."

---

## 7. Tabletop Exercises

- **Phase 1:** one tabletop in **Week 14** before launch. Scenario: leaked Ed25519 signing key.
- **Phase 2:** quarterly.
- Record scenario + response in `docs/operations/tabletops/YYYY-QN-<scenario>.md`.

---

## 8. Escalation Contacts (Phase 1)

| Role | Contact |
|---|---|
| On-call | Founder (SMS + email) |
| Backup / technical executor | Named in `EXECUTOR.md` (sealed) |
| Legal counsel | Named in `LEGAL-CONTACTS.md` (EU + TR retainer) |
| Stripe support | dashboard chat + `support@stripe.com` |
| Cloudflare support | dashboard + enterprise chat (if Pro tier) |
| Supabase support (Phase 2+) | dashboard |

---

## 9. Customer Communication

### 9.1 Status page

Simple static page at `status.piano-practice.<tld>` (Cloudflare Worker generating JSON → static render). Updated manually by founder during SEV-1.

### 9.2 Email templates

`docs/security/IR-TEMPLATES/` covers:

- Initial breach notification (72 h).
- Follow-up with remediation details.
- All-clear + apology + consideration (e.g., refund or extended access).

---

## 10. Post-Incident Review

Within **5 working days** after SEV-1 or SEV-2:

- Write the incident record (§3).
- Share lessons with self (or team in Phase 2+).
- Open follow-up tasks with owners and deadlines.
- Amend this runbook if the response revealed a gap.

---

## 11. References

- `MASTER-PLAN.md §4.5`
- [`SLO.md`](SLO.md)
- [`DR.md`](DR.md)
- [`../security/THREAT-MODEL.md`](../security/THREAT-MODEL.md)
- GDPR Art. 33; KVKK Art. 12

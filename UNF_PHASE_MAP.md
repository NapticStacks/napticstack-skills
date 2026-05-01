# UNF Phase Map

> The cross-reference between **UNF engagement phases** (the consulting playbook in [`NapticStacks/prompts`](https://github.com/NapticStacks/prompts), 30+ numbered phase READMEs) and **gstack specialist skills** (the per-feature sprint engine at [`garrytan/gstack`](https://github.com/garrytan/gstack), 40+ skills). This map is doc-only — there is no runtime coupling between UNF and gstack. It exists so that consultants reading a UNF phase know which gstack skills to invoke, and so that engineers running a gstack skill know which engagement phase the work belongs to.

The two cross-phase overlay wrappers — `/naptic-pickup` (session-start, reads the Team #1s board) and `/naptic-review` (PR review, surfaces the Jordan Approval Queue) — apply across phases and are not enumerated in the per-phase column. See `skills/naptic-pickup/SKILL.md` and `skills/naptic-review/SKILL.md` for invocation.

---

## Table 1 — UNF phase → gstack skills

One row per UNF phase. Phases without a current mapping leave the skill columns as `-`; future overlay work fills them in.

| Phase | Phase name | Primary gstack skills | Supporting gstack skills | Overlay wrapper |
|---|---|---|---|---|
| 00 | tailored-plan-of-action | `/office-hours`, `/plan-ceo-review` | `/plan-eng-review` | - |
| 00a | engagement-setup | - | - | - |
| 00b | roi-north-star | - | - | - |
| 00c | kickoff-prep-brief | - | - | - |
| 01 | project-onboarding | `/office-hours`, `/plan-ceo-review` | `/plan-eng-review` | - |
| 01a | requirements-extraction | - | - | - |
| 01b | current-state-audit | - | - | - |
| 01c | client-discovery | - | - | - |
| 01d | discovery-narrative | - | - | - |
| 02 | bedrock-architecture | - | - | - |
| 02a | rag-knowledge-base | - | - | - |
| 02b | blueprint-ingestion | - | - | - |
| 02c | interactive-demo-feedback | - | - | - |
| 02d | client-simulation-hub | - | - | - |
| 03 | lens-audit | `/cso` | `/review`, `/investigate` | - |
| 04 | gastown-batch-remediation | `/qa`, `/review` | `/design-review`, `/benchmark` | `/naptic-review` |
| 05 | multi-env-deployment | `/tenant-register`, `/register-instance` | - | - |
| 05-tenant-factory-registration | tenant-factory-registration | `/tenant-register`, `/register-instance` | - | - |
| 06 | environment-cost-audit | - | - | - |
| 06a | cost-audit-optimization | - | - | - |
| 07 | multi-instance-lifecycle | - | - | - |
| 07a | multi-instance-implementation | - | - | - |
| 08 | open-item-remediation | - | - | - |
| 09 | first-dev-deployment | - | - | - |
| 09b | post-deploy-audit | `/canary`, `/document-release` | `/landing-report` | - |
| 09c | deployment-readiness-audit | `/cso` | `/review`, `/investigate` | - |
| 09d | production-go-live | `/canary`, `/document-release` | `/landing-report` | - |
| 10 | sprint-planning | `/autoplan`, `/plan-ceo-review` | - | - |
| 11 | sprint-execution | `/qa`, `/review` | `/design-review`, `/benchmark` | `/naptic-review` |
| 11a | testing-acceleration | `/autoplan` → `/ship` → `/canary` | `/qa` | - |
| 12 | shared-hub-tenant-onboarding | `/tenant-register`, `/register-instance` | - | - |
| 13 | frontend-testing | `/qa`, `/review` | `/design-review`, `/benchmark` | `/naptic-review` |
| 14 | portal-blueprint | - | - | - |

---

## Table 2 — gstack skill → UNF phases

Reverse direction. One row per gstack skill that maps to anything. Alphabetical. Use this view when you invoke a gstack skill and want to know "which engagement phase am I working in."

| gstack skill | UNF phases this skill runs inside |
|---|---|
| `/autoplan` | 10, 11a |
| `/benchmark` | 04, 11, 13 (supporting) |
| `/canary` | 09b, 09d, 11a |
| `/cso` | 03, 09c |
| `/design-review` | 04, 11, 13 (supporting) |
| `/document-release` | 09b, 09d |
| `/investigate` | 03, 09c (supporting) |
| `/landing-report` | 09b, 09d (supporting) |
| `/office-hours` | 00, 01 |
| `/plan-ceo-review` | 00, 01, 10 |
| `/plan-eng-review` | 00, 01 (supporting) |
| `/qa` | 04, 11, 13, 11a (supporting) |
| `/register-instance` | 05, 05-tenant-factory-registration, 12 |
| `/review` | 03 (supporting), 04, 09c (supporting), 11, 13 |
| `/ship` | 11a |
| `/tenant-register` | 05, 05-tenant-factory-registration, 12 |

---

## How this is maintained

This mapping is **doc-only** — there is no runtime coupling, no CI check, no schema validator. It is a hand-edited cross-reference, updated at the **end of each engagement** when the team has fresh evidence about which gstack skills actually got used in which UNF phase.

When updating:

1. Check Table 1 row for the phase you're touching. Add/remove primary or supporting skills based on what was actually used.
2. Mirror the change in Table 2's reverse direction so the two tables stay consistent.
3. Run the round-trip check (Verification §1 in `/home/jmay/.claude/plans/unf-phase-map-overlay-skills.md`) to catch drift between this file and the per-phase READMEs in `NapticStacks/prompts`.
4. If a UNF phase row gets its first non-empty skill mapping, also append a `## Skills used in this phase` section to `prompts/<phase>/README.md` with a trailer pointing back here.

The mapping is intentionally conservative — phases without a confident skill match get `-` rather than a guess. Future overlay work fills them in as evidence accumulates. Empty does not mean "skip"; it means "we have not yet decided which gstack skills serve this phase."

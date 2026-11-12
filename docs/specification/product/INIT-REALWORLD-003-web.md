# INIT-REALWORLD-003 — spec slice for realworld-web

| Field | Value |
|-------|-------|
| Initiative | INIT-REALWORLD-003 |
| PRD | `realworld-meta/prd/INIT-REALWORLD-003.md` |
| PRD digest (H1) | `sha256:5cccf07066a0333027279aac0cae64d3f3be10773b7a5b37106302f2bbb9ceb8` |
| Meta PR | https://github.com/drivestream-lab/realworld-meta/pull/10 |
| Meta PR approved head (G1) | `abfbbcff877a83c298776fd4801b8ac4c75ddfba` |
| Impact map | `realworld-meta/prd/reports/Impact-Map-INIT-REALWORLD-003.md` |
| Impact-map revision (H3) | `1` |
| Repo scope digest (H2) | N/A — `drivestream-lab/realworld-web` is **deferred** (not affected); no `scope_digest` minted for this repo in map revision 1 |
| Tech-lead approval | Review `PRR_kwDOVac9Rs8AAAABXCDSZQ` / id `5840622181` by `adh-reviewer[bot]` (Gate‑1 attestation by adh.pe), APPROVED at `2026-11-10T10:48:57Z`; `commit_id` = `abfbbcff877a83c298776fd4801b8ac4c75ddfba` (matches merged meta PR #10 head; label `impact-map-lgtm`) |
| Repo | drivestream-lab/realworld-web |
| Date | 2026-11-12 |
| Status | **Gate closed — out of scope this INIT** (impact-map deferred; no product REQs for this repo) |

> **H4 citations:** The H1–H3 (and G1) rows above are the durable authority
> carrier for mid-lane freshness. Feas / TDD / plan digests are walk-time only
> and may be purged at initiative closure — see
> `prayog-skills/references/artifact-write-contract.md`.
>
> **T0 stop:** `/spec-draft` requires this repo to be `affected` with an H2
> `scope_digest` and not deferred/blocked. Impact-map revision 1 lists
> `realworld-web` under **Deferred repositories** (settings / any web UI for
> parked-search stewardship is an explicit PRD non-goal; no CTR consumer seam).
> Sole affected product owner is `drivestream-lab/realworld-api`
> (`INIT-REALWORLD-003-api.md`). No web functional REQs are drafted here.

## Overview

`realworld-web` has **no in-scope product delivery** for INIT-REALWORLD-003 under the approved impact map. Replay-ready personal search memory (park / recall / relabel / discard with query-string fidelity and owner isolation) is owned and proven on the machine-consumable API surface of `realworld-api`. Settings UI and any other web stewardship chrome for parked searches are an explicit non-goal this INIT.

**Out of this repo (this INIT):** all CAP-01..CAP-03 / REQ-01..REQ-13 product behavior — provider `realworld-api`. Optional local compose/env hosting for API prove — `realworld-local` (also deferred). Share/public parks, auto-run, ranking product, INIT-001 favorites, INIT-002 subscriptions — unchanged non-goals.

**Ownership:** this artifact records the Gate 1 scope stop for `realworld-web` only. It does **not** invent web REQs, UI acceptance, or architecture. Revisit when a later INIT (or map revision) promotes web into Affected with a new `scope_digest`.

## Functional requirements

| ID | Requirement | PRD source | Condition / event | Observable result | Evidence layer |
|----|-------------|-----------|-------------------|-------------------|----------------|
| — | None — this repo is impact-map **deferred**; no web REQs this INIT | Impact-map §3 Deferred; PRD §3 Non-goals (settings / any web UI); PRD §8 (no cross-repo CTR) | N/A | N/A — no web product surface to accept | N/A |

> **Id convention:** `REQ-*` is canonical. No `REQ-*` rows are minted for a deferred repo.
>
> Do not treat empty REQ table as “pass with zero work” for Forge — D1 FAIL closes the gate.

## Capabilities (carried from PRD)

| ID | Capability | Journeys | Notes |
|----|------------|----------|-------|
| CAP-01 | Park a personal search intent | J-01, J-02, J-08, J-12 | **Out of repo** — owned by `realworld-api` (web deferred) |
| CAP-02 | Recall and maintain own search memory | J-01, J-02, J-03, J-04, J-05, J-08, J-10 | **Out of repo** — owned by `realworld-api` (web deferred) |
| CAP-03 | Private continuity isolation | J-06, J-07, J-09, J-11 | **Out of repo** — owned by `realworld-api` (web deferred) |

## Journeys (carried from PRD)

| ID | Actor | Trigger | Main flow | Primary edge | Abandon | Caps |
|----|-------|---------|-----------|--------------|---------|------|
| J-01 | Seated member | Named park for later reuse | Park query + label → recall fidelity | Duplicate query allowed (OQ-06 default) | Abandon before park | CAP-01, CAP-02 — **out of repo** (`realworld-api`) |
| J-02 | Seated member | Unlabeled park | Park query, no label → recall | — | Abandon → no park | CAP-01, CAP-02 — **out of repo** |
| J-03 | Seated member | Continue prior search | Own-recall with query text | Empty list valid | Leave unchanged | CAP-02 — **out of repo** |
| J-04 | Seated member | Clearer mnemonic | Relabel → query unchanged | Clear label (OQ-07 default) | Abandon mid-relabel | CAP-02 — **out of repo** |
| J-05 | Seated member | Discard park | Discard → absent from recall | — | — | CAP-02 — **out of repo** |
| J-06 | Peer member | Harvest another’s memory | List/access A’s parks | Denied / no disclosure | — | CAP-03 — **out of repo** |
| J-07 | Guest / post-logout | Stewardship without seat | Park/list/relabel/discard | Fail closed | — | CAP-03 — **out of repo** |
| J-08 | Seated member | Continuity across sessions | Park → logout → login → recall | Same query text | — | CAP-01, CAP-02 — **out of repo** |
| J-09 | Peer member | Alter another’s park | Relabel/discard A’s park | A unchanged | — | CAP-03 — **out of repo** |
| J-10 | Seated member | Last discard | Discard final → empty recall | Empty valid | — | CAP-02 — **out of repo** |
| J-11 | Seated member | Bad park identity | Relabel/discard missing/non-owned | Fail closed | — | CAP-03 — **out of repo** |
| J-12 | Seated member | Empty query park | Park empty/whitespace | Refused; recall unchanged | — | CAP-01 — **out of repo** |

## Negative and failure paths

| REQ | Condition | Required behavior | Why it matters | Evidence |
|-----|-----------|-------------------|----------------|----------|
| — | N/A for this repo | No web failure paths this INIT — API owns seat/isolation kill paths | Prevents inventing UI acceptance for a deferred consumer | N/A |

## Out of scope for this repo

- **All** INIT-REALWORLD-003 product CAPs/REQs — provider `drivestream-lab/realworld-api` (affected; H2 `sha256:6e1a5775a890e03ad0295e43e795e36962fd431132a03412cf647282dc8331c7`)
- Settings / any web UI list or management for parked-search stewardship (PRD non-goal; impact-map deferral reason)
- Cross-repo CTR consumer seam (PRD §8: none)
- Local compose/env delta for API prove — `drivestream-lab/realworld-local` (also deferred)
- Share/public/collaborative parks; auto-run; ranking product; INIT-001 favorites; INIT-002 subscriptions; comments; OAuth; email verification; mobile/native; admin tooling

**Revisit condition (impact-map §3):** Later INIT that adds settings (or other) UI for search-memory stewardship — new map revision with web `affected` + `scope_digest`.

## Cross-service contracts

None — no cross-repository boundary this INIT (PRD §8; impact-map §6). Stewardship owned and proven on `realworld-api` only.

## Non-functional requirements

| Area | Requirement or N/A rationale | Acceptance / evidence |
|------|------------------------------|-----------------------|
| Security | N/A this repo — seat/peer isolation is API-owned (PRD REQ-04, REQ-10–REQ-13) | N/A |
| Reliability | N/A — no web CAP this INIT | N/A |
| Performance / capacity | N/A — no web CAP this INIT | N/A |
| Observability | N/A — no web CAP this INIT | N/A |
| Privacy / data handling | N/A this repo — private parks must not be exposed via web UI this INIT because no web surface is in scope | N/A |
| Migration / compatibility | N/A — no web product delta | N/A |
| Rollback / recovery | N/A — no web product delta | N/A |
| Operations / support | N/A — no new web feature surface; no new infra | N/A |

## Assumptions

| ID | Assumption | Evidence | Owner | Status | Invalidated when |
|----|------------|----------|-------|--------|------------------|
| A-01 | Impact-map revision 1 deferral of `realworld-web` is authoritative for this `/spec-draft` invoke | Impact-map §3; PRD Lock 6 / §3 non-goals / §8 | PM | confirmed | Map revision promotes web to affected with H2 |
| A-02 | Lead “all impacted repos” listing does not override the committed impact-map disposition for this repo | Skill NON-NEGOTIABLE 7; impact-map SSOT for H2 | PE | confirmed | Approved map revision changes web disposition |
| A-03 | API sibling owns CAP-01..CAP-03 prove; web must not mint competing REQs while deferred | Impact-map §2 Affected = api only; existing `INIT-REALWORLD-003-api.md` | PE | confirmed | Web promoted to affected |

## Spec questions (ambiguities — need PM or domain confirmation before feasibility)

| ID | Lane | Question | Owner | Blocking | Required by | Default if deferred | Status | Resolution link |
|----|------|----------|-------|----------|-------------|---------------------|--------|-----------------|
| Q-01 | PM | Confirm `realworld-web` remains deferred for INIT-REALWORLD-003 (no settings/UI CAP) vs promote to affected in a map revision | PM | yes | Gate 1 / next map | Remain deferred; no web REQs; API-only INIT | open — gate closed on current map | Impact-Map §3 Deferred; revisit condition |
| Q-02 | PE | As-built index `docs/specification/as-built/implementation-status.md` is absent in this workspace | PE | no | later when web promoted | Treat as empty as-built until index exists; does not unblock deferred gate | open | Missing path noted at T0 |

> **Bounded clarification (Hermes non-interactive):** Q-01 default locked from impact-map + PRD non-goal — remain deferred; do not invent web REQs. Q-02 non-blocking for this stop. Material gate = D1 deferred disposition → workflow `blocked` (not `pass`).

## Draft check summary (D1–D12)

| Check | Status | Evidence / findings |
|-------|--------|---------------------|
| D1 Approved handoff current | **FAIL** | H1 matches PRD (`sha256:5cccf070…`); H3 map_revision=1; G1 APPROVED `commit_id` = meta PR #10 head `abfbbcff…`; label `impact-map-lgtm` — **but** this repo is **deferred** (not affected); **no H2 `scope_digest`** for `realworld-web` |
| D1b No open blocking IM-* | PASS | Impact-map §10: IM-01..IM-07 are `Blocking: no`; Gate 1 note allows defaults for api draft — irrelevant to web deferred stop |
| D2 Complete PRD traceability | N/A → PASS rationale | No in-scope web REQs; CAP-01..CAP-03 carried as out-of-repo N/A |
| D2b Journeys carried | PASS | J-01..J-12 listed with owning repo `realworld-api` / out-of-repo |
| D3 Repo-bounded scope | PASS | Zero web REQs; deferral + revisit condition match impact-map §3 |
| D4 Observable acceptance | N/A → PASS rationale | No applicable web REQ rows |
| D5 Negative/failure paths | N/A → PASS rationale | No web failure surface this INIT |
| D6 Assumptions/questions | PASS | A-01..A-03 confirmed; Q-01 blocking recorded; Q-02 non-blocking |
| D7 Cross-repository contracts | PASS | None — PRD §8 / impact-map §6 |
| D8 NFR applicability | PASS | All eight areas N/A with reason |
| D9 As-built alignment | NEEDS INPUT (non-gating here) | As-built index absent; source not scanned for inventing REQs because D1 already FAIL |
| D10 Dependency order | PASS | Matches impact-map §7: api only; web deferred |
| D11 Zero unresolved blockers | **FAIL** | Q-01 Blocking=yes while map keeps web deferred — gate closed until map promotes web or human accepts out-of-scope stop |
| D12 Output completeness | PASS | Header H1/H3/G1 + deferred H2 N/A, tables, check summary, outcome, PR readiness, handoff present |

**Draft verdict:** FAIL

**Selected workflow outcome:** `blocked`
**Outcome reason:** Explicit Gate 1 closure — `realworld-web` is deferred (not affected, no H2) on approved impact-map revision 1; D1 FAIL; do not draft in-scope web REQs or fill `handoff.forge` for `open_draft_pr`.

Do not advance to `/initiative-feasibility` unless the workflow outcome is
`pass`, the draft verdict is PASS, and the developer review below is complete.

## PR readiness handoff

| Item | Value |
|------|-------|
| Workflow outcome | `blocked` — impact-map deferred this repo; D1 FAIL (no H2) |
| Verdict | PR BLOCKED |
| Existing spec PR | none |
| Proposed branch | N/A — do not open Draft spec PR while deferred |
| Proposed base | `develop` |
| Proposed title | N/A |
| PR type | N/A |
| Local artifacts to publish | `docs/specification/product/INIT-REALWORLD-003-web.md` (gate-stop record only) |
| Forge readiness | **omit** `handoff.forge` — outcome is not `pass`; do not recommend `/open-draft-pr` for this repo until map promotes web |
| Reviewer | N/A |
| Initial Gate 2 label | N/A |
| Additional invalidation label | none |
| Blocking items | Q-01 (web deferred on current map); D1 no H2 for this repo |

**No GitHub side effects have occurred.** This file is a durable out-of-scope / gate-stop record only. Next workflow node is `spec-human-decision` (human checkpoint). To unblock web product work: revise Impact-Map to `affected` with a new `scope_digest`, re-approve Gate 1, then re-run `/spec-draft`.

### Proposed Draft PR body

```markdown
N/A — PR BLOCKED (repo deferred on Impact-Map revision 1).
```

## Developer review

- [ ] Scope matches the approved impact-map repo disposition (**deferred** — no H2)
- [ ] No web REQs invented while deferred
- [ ] CAP/J carried as out-of-repo N/A with owning repo named
- [x] Blocking gate recorded (Q-01 / D1)
- [ ] Developer confirmed draft is ready for feasibility — **not applicable** while `blocked`

## After Draft PR creation

N/A while outcome is `blocked`. If a later map revision promotes this repo:

```bash
launchpad apply-gates --repo realworld-web --apply
```

| PE action | Remove | Add |
|-----------|--------|-----|
| Pending/new revision | `spec-lgtm`, `spec-blocked` | `spec-pending` |
| Request changes/hold | `spec-pending`, `spec-lgtm` | `spec-blocked` |
| Approve full package | `spec-pending`, `spec-blocked`, `spec-revised`, `spec-stale` | `spec-lgtm` |

## References

- PRD: `realworld-meta/prd/INIT-REALWORLD-003.md`
- Meta PRD PR: https://github.com/drivestream-lab/realworld-meta/pull/10
- Spec PR: none (blocked)
- Service profile: not present (`docs/specification/product/00-service-profile.md` absent)
- Impact map: `realworld-meta/prd/reports/Impact-Map-INIT-REALWORLD-003.md`
- Sibling API spec (affected): `realworld-api/docs/specification/product/INIT-REALWORLD-003-api.md`

```yaml
handoff:
  contract: sdd-delivery/v2
  stage: spec-draft
  outcome: blocked
  artifact:
    path: docs/specification/product/INIT-REALWORLD-003-web.md
  blockers:
    - Q-01
  signals:
    pr_ready: false
    draft_verdict: FAIL
    map_revision: 1
    meta_pr: https://github.com/drivestream-lab/realworld-meta/pull/10
    meta_pr_approved_head: abfbbcff877a83c298776fd4801b8ac4c75ddfba
    prd_digest: sha256:5cccf07066a0333027279aac0cae64d3f3be10773b7a5b37106302f2bbb9ceb8
    scope_digest: null
    repo_disposition: deferred
    d1: FAIL
    affected_owner: drivestream-lab/realworld-api
    codegraph_provider: degraded-none
    grounding_depth: none
  next_candidates:
    - spec-human-decision
  human_checkpoint: true
  external_action: false
```

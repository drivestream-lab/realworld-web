# INIT-REALWORLD-002 — spec slice for realworld-web

| Field | Value |
|-------|-------|
| Initiative | INIT-REALWORLD-002 |
| PRD | `realworld-meta/prd/INIT-REALWORLD-002.md` |
| PRD digest (H1) | `sha256:5a9b49dd102202262edca3cb3d2bf315f43782884372d945be9274124b974c92` |
| Meta PR | https://github.com/drivestream-lab/realworld-meta/pull/8 |
| Meta PR approved head (G1) | `ee9fe45ad23d988eface7517a3a65a7b58b592c1` |
| Impact map | `realworld-meta/prd/reports/Impact-Map-INIT-REALWORLD-002.md` |
| Impact-map revision (H3) | `1` |
| Repo scope digest (H2) | `sha256:63d4c2c76934a2659455417bb02885bcc61843a4c5d8b517bc5e966631f1e566` |
| Tech-lead approval | Review `PRR_kwDOVac9Rs8AAAABXAtAcQ` / id `5839208561` by `adh-reviewer[bot]` (Gate‑1 attestation by adh.pe), APPROVED at `2026-11-10T08:58:03Z`; `commit_id` = `ee9fe45ad23d988eface7517a3a65a7b58b592c1` (matches merged meta PR #8 head; label `impact-map-lgtm`) |
| Repo | drivestream-lab/realworld-web |
| Date | 2026-11-12 |
| Status | Draft — dev review required before Forge publish |

> **H4 citations:** The H1–H3 (and G1) rows above are the durable authority
> carrier for mid-lane freshness. Feas / TDD / plan digests are walk-time only
> and may be purged at initiative closure — see
> `prayog-skills/references/artifact-write-contract.md`.

## Overview

`realworld-web` is the **consumer UAT surface** for CAP-01..CAP-04 and CTR-01..CTR-04: guest ungated unpersonalized public discovery; seated author-subscription stewardship UI (viewer-relative boolean, reseat persistence); denial-honest empty and fail-closed denied outcomes; subscription-stream vs global/tag surfaces with lifecycle honesty (publish-in / delete-out / privacy) — proven against `realworld-api` in `realworld-local` compose.

**Out of this repo:** API provider truth and fail-closed persistence (`realworld-api`); local compose/DB join (`realworld-local`); follower/following counts as social proof; comments; OAuth; email verification; mobile/native clients; composed subscription∩tag discovery; reopening INIT-REALWORLD-001 seat/publish/favorites/nameplate requirement sets.

**Ownership:** this spec states observable web product behavior, acceptance, field meaning, invariants, errors, and compatibility only. Implementation architecture (React/Redux module layout, routing, token storage, tab chrome naming) is **not** decided here — routed to feasibility / technical review.

## Functional requirements

| ID | Requirement | PRD source | Condition / event | Observable result | Evidence layer |
|----|-------------|-----------|-------------------|-------------------|----------------|
| REQ-01 | Present the public article listing without requiring authentication | PRD REQ-01 / CAP-01 | Guest opens the public article listing | Listing state is visible (populated or empty) with no account wall | live (CAP-01, J-01) |
| REQ-02 | Open a published article without requiring authentication | PRD REQ-02 / CAP-01 | Guest selects a published article when at least one exists | Published content is readable while still unseated | live (CAP-01, J-01) |
| REQ-03 | Use public tag surfaces without requiring authentication | PRD REQ-03 / CAP-01 | Guest opens the tag catalog or a tag lane | Tag surface is usable; no account wall | live (CAP-01, J-02) |
| REQ-04 | Keep public discovery available when a subscribe affordance is visible | PRD REQ-04 / CAP-01; IM-05 | Guest views a public author or article surface that offers subscribe to seated actors | Guest can still complete public listing/open/tag without authenticating | live (CAP-01, J-09) |
| REQ-05 | Keep public discovery membership independent of the viewer’s subscriptions | PRD REQ-05 / CAP-01 | Seated member subscribes or unsubscribes, then re-opens public listing and a tag lane | Public listing and tag-lane memberships do not add/remove articles solely because subscriptions changed | live (CAP-01, CAP-04, J-14) |
| REQ-06 | Keep the seated global article listing subscription-independent | PRD REQ-06 / CAP-01 | Seated member opens the global article listing when published articles exist | Membership agrees with the named global-feed contract and does not depend on the viewer’s subscriptions | live (CAP-01, J-03) |
| REQ-07 | Subscribe to another author and show subscription state true in the UI | PRD REQ-07 / CAP-02 | Seated member subscribes to an author they do not already subscribe to | Viewer-relative subscription state for that author becomes subscribed; subsequent seated views agree | live (CAP-02, J-04) |
| REQ-08 | Unsubscribe from an author and show subscription state false in the UI | PRD REQ-08 / CAP-02 | Seated member unsubscribes from an author they currently subscribe to | Viewer-relative subscription state becomes not-subscribed; subsequent seated views agree | live (CAP-02, J-04) |
| REQ-09 | Persist subscriptions across unseat and reseat in the client | PRD REQ-09 / CAP-02 | Seated member subscribes to an author, logs out, then logs in again without changing edges while unseated | After reseat, viewer-relative subscription state for that author is still subscribed | live (CAP-02, J-13) |
| REQ-10 | Refuse self-subscribe in the UI | PRD REQ-10 / CAP-03; IM-09 | Seated member attempts to subscribe to themselves | Subscription graph unchanged; subscription state toward self does not become subscribed (chrome may hide or disable — either OK) | unit / live (CAP-03, J-07) |
| REQ-11 | Deny subscribe and unsubscribe while unseated at the product rule layer | PRD REQ-11 / CAP-03 | Guest or post-logout actor attempts subscribe or unsubscribe | No subscription-edge change; fail closed (not chrome-only) | live (CAP-03, J-10) |
| REQ-12 | Refuse subscribe of a missing or unknown author identity in the UI | PRD REQ-12 / CAP-03; IM-03 | Seated member attempts to subscribe to an author identity that does not exist | Subscription graph unchanged; fail closed | unit / live (CAP-03, J-12) |
| REQ-13 | Allow an empty subscription stream as a valid outcome in the UI | PRD REQ-13 / CAP-03 | Seated member subscribes to zero authors (or subscribed authors have zero published articles) and opens the subscription stream | Empty stream state is shown; must not silently show global public membership; must not present as an auth or hard error wall | live (CAP-03, CAP-04, J-08) |
| REQ-14 | Keep guest public discovery working after a member unseats | PRD REQ-14 / CAP-03 | Member who previously used subscriptions logs out, then browses public articles/tags as Guest | Public discovery still works without auth friction; subscription mutations remain denied | live (CAP-01, CAP-03, J-10) |
| REQ-15 | Present the subscription stream for the seated member | PRD REQ-15 / CAP-04 | Seated member who subscribes to at least one author that has published articles opens the subscription stream | Stream shows only articles from authors that member currently subscribes to | live (CAP-04, J-05) |
| REQ-16 | Remove an author’s articles from the subscription stream after unsubscribe | PRD REQ-16 / CAP-04 | Seated member unsubscribes from an author who had articles in that stream | Those articles no longer appear in the subscription stream; public/global membership is unchanged by unsubscribe alone | live (CAP-04, J-06) |
| REQ-17 | Include a subscribed author’s newly published article in the subscription stream | PRD REQ-17 / CAP-04 | Seated member already subscribes to an author; that author then publishes a new tagged article | The new article appears in the subscriber’s subscription stream on open/re-check; public listing/tag membership update as public-discovery rules require without depending on who subscribes to whom | live (CAP-04, J-11) |
| REQ-18 | Remove a deleted article from the subscription stream | PRD REQ-18 / CAP-04 | Seated member subscribes to an author; that author deletes a published article that was in the subscriber’s stream | That article no longer appears in the subscription stream on next open; Guest open-by-reference stays not-found per INIT-001 delete rules | live (CAP-04, J-16) |
| REQ-19 | Keep the subscription stream private to the subscriber in the UI | PRD REQ-19 / CAP-04 | Guest (or a peer who is not that subscriber) attempts to treat another member’s subscription stream as a public discovery surface | Other actors do not obtain that member’s stream membership as public truth; Guest public discovery remains the ungated surface | live (CAP-04, CAP-01, J-15) |
| REQ-20 | Keep tag-lane membership tag-true under subscription churn | PRD REQ-20 / CAP-04; IM-08 | Member subscribes or unsubscribes without changing article tags | Tag lanes still reflect tag membership only — not subscription state; no composed subscription∩tag third surface | live (CAP-04, CAP-01, J-14) |
| REQ-21 | Present global listing and subscription stream newest-first by publish/update time | PRD OQ-02 / IM-02 default; CAP-01 / CAP-04 | Guest or seated member opens global listing or subscription stream with ≥2 eligible articles | Listing order is newest-first by the article’s publish/update time (same rule as INIT-001 OQ-06 default) | integration / live (CAP-01, CAP-04, J-03, J-05) |
| REQ-22 | Show an empty tag lane for unknown or blank-normalized tags | PRD OQ-04 / IM-04 default; CAP-01 | Guest or member opens a tag lane for an unknown or blank-normalized tag | Empty lane; must not show unrelated articles; must not invent a distinct not-found wall beyond empty | live (CAP-01, J-02) |
| REQ-23 | Omit seated-only subscription state for Guest on public author presentation | PRD OQ-05 / IM-05 default; CAP-01 / CAP-02 | Guest views another member’s public author presentation | Seated subscription boolean / another member’s edges are not presented as Guest truth; subscribe affordance may be visible without gating public discovery (REQ-04) | live (CAP-01, CAP-02, J-09, J-15) |
| REQ-24 | Drive author-subscription and denial-honest empty kill paths against the local API provider | Impact-map web scope; CAP-01..CAP-04 | UAT observer runs guest ungated discovery → seated subscribe/stream → empty/denied honesty → unseat denial on web against API in local compose | Kill paths are demonstrable end-to-end on this client; API root used for prove is the local provider (not a third-party demo host) | live (J-01, J-05, J-08, J-10) |

> **Id convention:** `REQ-*` is canonical. Legacy display alias `FR-{nn}` ≡ `REQ-{nn}` — prefer `REQ-*`.
>
> **Behavioral acceptance vs evidence:** Condition/event + observable result are
> the product acceptance statement (implementation-neutral). Evidence layer
> names how it will be proved later. Live evidence cites `CAP-*` and/or `J-*`.

## Capabilities (carried from PRD)

| ID | Capability | Journeys | Notes |
|----|------------|----------|-------|
| CAP-01 | Ungated unpersonalized public discovery | J-01, J-02, J-03, J-09, J-10, J-14 | Consumer of CTR-01 / CTR-04; never requires a seat; membership independent of subscriptions |
| CAP-02 | Seat-bound author-subscription stewardship | J-04, J-06, J-07, J-10, J-12, J-13 | Consumer of CTR-02; viewer-relative boolean only (IM-01); persistence across reseat |
| CAP-03 | Denial-honest empties and fail-closed outcomes | J-07, J-08, J-10, J-12, J-15 | Empty stream/listing/lane valid; unseated/self/missing denials fail closed; no silent global-fill |
| CAP-04 | Subscription-stream lifecycle honesty | J-05, J-06, J-08, J-11, J-14, J-15, J-16 | Consumer of CTR-03; stream ⊆ subscribed authors’ published articles; private to subscriber; publish-in / delete-out |

## Journeys (carried from PRD)

| ID | Actor | Trigger | Main flow | Primary edge | Abandon | Caps |
|----|-------|---------|-----------|--------------|---------|------|
| J-01 | Guest | Wants public articles without signing in | Opens public listing → opens a published article when present → reads | Empty listing → still ungated | Leaves without registering | CAP-01 |
| J-02 | Guest | Explores topics | Opens tag catalog/lane → scans → opens a matching article when present | Empty lane for that tag only | Leaves tags | CAP-01 |
| J-03 | Seated member | Checks unpersonalized global listing | Opens global article listing → confirms membership matches named global-feed contract and is not subscription-filtered | Empty global listing still readable | Leaves listing | CAP-01 |
| J-04 | Seated member | Builds / clears a subscription | Opens an author’s public presentation → subscribes → sees subscription state true → unsubscribes → sees false | Self-subscribe attempt → refused (enters J-07) | Cancels without subscribe | CAP-02 |
| J-05 | Seated member | Uses the subscription stream | Subscribes to an author who has published articles → opens subscription stream → opens one such article | If author has no articles → empty stream (J-08) | Unsubscribes (enters J-06) | CAP-04 |
| J-06 | Seated member | Proves stream honesty after unsubscribe | Unsubscribes from an author → subscription state false → stream loses that author’s articles → global listing unchanged by unsubscribe alone | Last unsubscribe → empty stream | N/A — unsubscribe is the act | CAP-04, CAP-02 |
| J-07 | Seated member | Attempts self-subscribe | Tries to subscribe to own author identity | Graph unchanged; denial observed | Returns to other authors | CAP-03, CAP-02 |
| J-08 | Seated member | Opens stream with nothing to show | Subscribes to zero authors (or subscribed authors silent) → opens subscription stream | Empty state; must not silently show global membership; not an error wall | Switches to global listing | CAP-03, CAP-04 |
| J-09 | Guest | Sees subscribe affordance while browsing | Views public author/article surface that offers subscribe to seated actors → continues public discovery without authenticating | If Guest tries subscribe → denied (J-10) | Leaves | CAP-01 |
| J-10 | Seated → Guest | Unseats after using subscriptions | Subscribes to someone → logs out → completes public listing/open as Guest → attempts subscribe/unsubscribe | Public discovery works; mutations fail closed | Reseats (J-13) | CAP-01, CAP-03, CAP-02 |
| J-11 | Author + Subscriber | Subscribed author publishes | Subscriber already subscribes to Author → Author publishes new tagged article → Subscriber opens subscription stream | New article in stream; public/tag update as ungated discovery for all readers | Subscriber unsubscribes first | CAP-04 |
| J-12 | Seated member | Targets a missing author | Attempts subscribe on unknown author identity | Fail closed; graph unchanged (IM-03) | Returns to known authors | CAP-03 |
| J-13 | Seated member | Proves subscription persistence across reseat | Subscribes to an author → logs out → logs in → checks subscription state (and stream if author has articles) | State still subscribed; mutations were denied while unseated | Abandons reseat | CAP-02 |
| J-14 | Seated member | Proves public/tag stay unpersonalized under churn | Subscribes/unsubscribes while checking public listing, global listing, and a tag lane | Memberships unchanged by subscriptions alone | Stops probing | CAP-01, CAP-04 |
| J-15 | Guest + Peer | Prove stream privacy | Guest (and/or a peer who is not the subscriber) attempts to treat Member A’s subscription stream as public discovery | A’s stream membership is not revealed as public truth; Guest stays on ungated public discovery | Stops probing | CAP-04, CAP-03 |
| J-16 | Author + Subscriber | Subscribed author deletes | Subscriber already has Author’s article in stream → Author deletes that article → Subscriber opens subscription stream | Article gone from stream; Guest open-by-reference not-found | Subscriber unsubscribes first | CAP-04 |

## Negative and failure paths

| REQ | Condition | Required behavior | Why it matters | Evidence |
|-----|-----------|-------------------|----------------|----------|
| REQ-01 / REQ-03 | Empty bulletin / empty tag catalog | Empty states still ungated | Frictionless ≠ must have content | live (J-01, J-02) |
| REQ-04 / REQ-23 | Subscribe CTA visible to Guest; Guest on public author | Public discovery still ungated; seated subscription state omitted for Guest | Prevents soft auth walls and peer-edge leakage | live (J-09, J-15) |
| REQ-05 / REQ-06 / REQ-20 | Subscription graph changes | Public/global/tag memberships do not silently become subscription-filtered; no composed ∩ surface | Unpersonalized discovery kill path | live (J-14) |
| REQ-13 | Subscribing nobody / subscribed authors silent | Empty stream; not an error wall; not silently filled from global | Denial-honest empty kill path | live (J-08) |
| REQ-16 / REQ-08 | After unsubscribe | Stream loses articles; subscription state false; global untouched by unsubscribe alone | Stream honesty | live (J-06) |
| REQ-10 | Self-subscribe attempt | Fail closed; chrome hide or disable OK | Graph integrity | unit / live (J-07) |
| REQ-11 / REQ-14 | Unseated subscribe cluster | Fail closed; guest public discovery remains open | Seat boundary + kill path | live (J-10) |
| REQ-12 | Subscribe missing/unknown author | Fail closed; graph unchanged | No ghost edges | unit / live (J-12) |
| REQ-09 | Logout then login | Subscriptions still present after reseat; mutations still denied while unseated | Persistence ≠ keep seat | live (J-13) |
| REQ-17 | Subscribed author publishes | Stream gains article on open/re-check; public/tag update as discovery | Lifecycle honesty | live (J-11) |
| REQ-18 | Subscribed author deletes | Stream loses article on next open; no ghost readable body via stream | Lifecycle honesty + INIT-001 delete coherence | live (J-16) |
| REQ-19 | Guest/peer probing another’s stream | No public reveal of that stream membership | Stream privacy | live (J-15) |
| REQ-22 | Unknown / blank-normalized tag | Empty lane; no unrelated articles | Tag-true discovery | live (J-02) |

## Out of scope for this repo

- API provider persistence, subscription graph source of truth, and fail-closed server rules — **realworld-api**
- Local compose / DB join for live prove — **realworld-local**
- Follower / following **counts** as social proof (IM-01 / OQ-01 default: boolean only)
- Composed “subscription ∩ tag” third discovery surface (IM-08)
- Comments (legacy UI may exist under `src/components/Article/*Comment*`; not a CAP/REQ here)
- OAuth / third-party identity; email verification gates
- Mobile or native clients
- Reopening INIT-REALWORLD-001 publish / seat / favorites / nameplate requirement sets
- Public who-favorited social proof
- Mutual-follow ceremony or “friends” packaging beyond directed subscriptions
- Mid-view push without re-open for last-unsubscribe empty chrome or delete-out (IM-06 / IM-11 deferred)
- Treating clarification rally / Buzz / OKF as product CAPs

## Cross-service contracts

| Contract ID | Provider / owner | Consumer / owner | Entry point | Input shape | Output shape | Invariants | Errors | Compatibility / versioning | Contract-test location |
|-------------|------------------|------------------|-------------|-------------|--------------|------------|--------|----------------------------|------------------------|
| CTR-01 | realworld-api / @drivestream-lab/backend-devs | realworld-web / @drivestream-lab/frontend-devs | Read ungated unpersonalized public discovery | Listing mode (global / public), article reference, public author presentation, tag identity | Public listing, single published article, public tag membership, public author presentation; empty sets allowed | Reading never requires a seat; only published articles appear; empty listing/lane allowed; membership does not depend on viewer subscriptions; newest-first (REQ-21) | Missing article → not-found; empty sets allowed | New INIT-002 seam on INIT-001 bulletin baseline; breaking change requires INIT revision | planned under `tests/verify` / consumer contract suite (TBD in plan) |
| CTR-02 | realworld-api / @drivestream-lab/backend-devs | realworld-web / @drivestream-lab/frontend-devs | Manage author subscriptions and report viewer-relative state | Subscribe / unsubscribe target author identity; seated session proof | Viewer-relative subscription boolean; mutation confirmation | Seat required to mutate; self-subscribe refused; missing author refused; directed edges; state true/false must match edge; edges persist across unseat/reseat; unseated mutate denied; counts not required (IM-01) | Unauthenticated; self target; missing author | New INIT-002 seam | planned under `tests/verify` (TBD in plan) |
| CTR-03 | realworld-api / @drivestream-lab/backend-devs | realworld-web / @drivestream-lab/frontend-devs | Read subscription stream | Seated session; optional pager | Seated-only listing of published articles by currently subscribed authors; empty allowed | Stream ⊆ articles authored by currently subscribed authors; empty stream allowed; must not silently equal global membership when subscriptions are empty; stream is not a public discovery surface for other actors; newest-first (REQ-21) | Unauthenticated; empty sets allowed | New INIT-002 seam | planned under `tests/verify` (TBD in plan) |
| CTR-04 | realworld-api / @drivestream-lab/backend-devs | realworld-web / @drivestream-lab/frontend-devs | Keep tag-true discovery under subscription churn | Tag catalog / per-tag article membership request | Tag catalog and per-tag article membership | Lane membership reflects current tags only; independent of subscriptions; empty lane allowed (including unknown/blank-normalized → empty per REQ-22); no silent subscription∩tag composition | Unknown tag → empty lane (IM-04) | New INIT-002 seam | planned under `tests/verify` (TBD in plan) |

## Non-functional requirements

| Area | Requirement or N/A rationale | Acceptance / evidence |
|------|------------------------------|-----------------------|
| Security | Subscribe/unsubscribe fail closed while unseated (REQ-11). Self-subscribe refused (REQ-10). Guest public discovery must not be gated by auth (REQ-01–REQ-04, REQ-14). Missing-author subscribe refused (REQ-12). Subscription stream not a public reveal surface (REQ-19). | unit + live denial / privacy paths |
| Reliability | N/A beyond fail-closed UAT behavior — not a multi-region SLA INIT. Incomplete subscribe/unsubscribe must not leave partial success observable in the UI. | inspection / negative-path tests |
| Performance / capacity | N/A for this INIT; no concurrent-load target locked. | N/A |
| Observability | N/A as product REQ — client logging remains an engineering concern; Buzz/coach signal is programme process. | N/A for product acceptance |
| Privacy / data handling | Viewer-relative subscription boolean in scope; follower/following counts out of scope (IM-01). Guest must not be shown another viewer’s seated subscription state or stream as authoritative truth (REQ-19, REQ-23). No who-favorited proof. | live (J-09, J-15) |
| Migration / compatibility | Builds on INIT-REALWORLD-001 bulletin/seat baseline; subscriptions are additive social state. Stream lifecycle must stay coherent with INIT-001 publish/delete. Legacy gothinkster Conduit under `src/` includes follow/feed chrome that is baseline source, not guaranteed as-built until proven. | as-built / feasibility |
| Rollback / recovery | N/A as product REQ — delivery rollback is engineering. | N/A |
| Operations / support | Live prove environment is `realworld-local` compose (out of this repo); web must target local API for prove (REQ-24). No 24×7 support envelope locked. | live kill-path prove |

## Assumptions

| ID | Assumption | Evidence | Owner | Status | Invalidated when |
|----|------------|----------|-------|--------|------------------|
| A-01 | INIT-REALWORLD-001 seat/bulletin rules stay in force; this INIT does not weaken guest read or seated contribution gates | PRD A-01 | PM | confirmed (Wave default applied) | Lead reopens INIT-001 conflicting REQs |
| A-02 | Named RealWorld programme contracts for global feed, following feed, tags, and follow edges exist as api↔web meaning; “follow” maps to author subscription here | PRD A-02; Impact-Map CTR-01..CTR-04 | PM | confirmed | Named contract missing → block seam row; do not invent eng payloads |
| A-03 | “Auth friction” means any requirement to authenticate before completing public listing/tag/open | PRD A-03 | PM | confirmed | Soft walls that block public discovery are accepted |
| A-04 | Empty subscription stream is valid and must not silently fall back to global membership | PRD A-04 | PM | confirmed | Product later locks a silent fallback |
| A-05 | Viewer-relative subscription boolean is sufficient; counts deferred (IM-01) | PRD A-06; IM-01 | PM | confirmed | Lead requires counts as social proof |
| A-06 | Logout unseats but does not wipe the membership’s subscription graph; reseat restores the same edges | PRD A-07 | PM | confirmed | Lead wants ephemeral subscriptions-per-session |
| A-07 | Subscription-stream publish-in and delete-out are required; mid-view push without re-open is not required (IM-06 / IM-11 defaults) | PRD A-08; IM-06; IM-11 | PM | confirmed | Lead requires live push without re-check |
| A-08 | As-built index is absent; source under `src/` is legacy gothinkster React+Redux Conduit with Follow/Unfollow (`Profile.js`), “Your Feed” / `Articles.feed` (`Home/MainView.js`), and `agent.js` defaulting to third-party demo API (overlay rewrites to `http://localhost:3100/api`) | Source scan 2026-11-12; empty `docs/specification/as-built/`; no `00-service-profile.md`; no ADRs | PE | open | As-built index published with capability rows |

## Spec questions (ambiguities — need PM or domain confirmation before feasibility)

| ID | Lane | Question | Owner | Blocking | Required by | Default if deferred | Status | Resolution link |
|----|------|----------|-------|----------|-------------|---------------------|--------|-----------------|
| Q-01 | PM | Public follower/following counts vs viewer-relative boolean only (OQ-01 / IM-01) | PM | no | feasibility | Boolean subscription state only; counts out of scope | resolved | Impact-Map IM-01 → Out of scope / CTR-02 / A-05 |
| Q-02 | PM | Ordering rule for global listing vs subscription stream (OQ-02 / IM-02) | PM | no | feasibility | Newest-first by same publish/update time rule as INIT-001 OQ-06 | resolved | Impact-Map IM-02 → REQ-21 |
| Q-03 | PM | Subscribe/unsubscribe targeting missing or deactivated author (OQ-03 / IM-03) | PM | no | feasibility | Fail closed; graph unchanged | resolved | Impact-Map IM-03 → REQ-12 |
| Q-04 | PM | Unknown tag: empty lane vs distinct not-found (OQ-04 / IM-04) | PM | no | feasibility | Empty lane for unknown/blank-normalized tags | resolved | Impact-Map IM-04 → REQ-22 |
| Q-05 | PM | Guest observation of another viewer’s subscription state on public profiles (OQ-05 / IM-05) | PM | no | feasibility | Omit seated-only subscription state for Guest; affordance may show without gating discovery | resolved | Impact-Map IM-05 → REQ-04 / REQ-23 |
| Q-06 | PM | Last-unsubscribe empty-stream chrome without re-open (OQ-06 / IM-06) | PM | no | later | Same visit after re-check/re-open is enough | open | Impact-Map IM-06 |
| Q-07 | PM | Clarification rounds counted per CTR vs per product PR thread (OQ-07 / IM-07) | PM | no | later | Per product PR conversation; ≤2 rounds total (programme process; not a web REQ) | open | Impact-Map IM-07 |
| Q-08 | PM | Composed subscription ∩ tag surface (OQ-08 / IM-08) | PM | no | feasibility | Separate surfaces only; no composed third surface | resolved | Impact-Map IM-08 → REQ-20 / Out of scope |
| Q-09 | PM | Own-author self-subscribe affordance chrome (OQ-09 / IM-09) | PM | no | feasibility | No successful self-subscribe path; hide or disable both fine if REQ-10 holds | resolved | Impact-Map IM-09 → REQ-10 |
| Q-10 | PM | Already-subscribed subscribe attempt distinguishability (OQ-10 / IM-10) | PM | no | later | Both leave state subscribed; no error required if graph unchanged | open | Impact-Map IM-10 |
| Q-11 | PM | Subscription-stream delete-out same-visit without re-open (OQ-11 / IM-11) | PM | no | later | Next open of the stream after delete is enough | open | Impact-Map IM-11 |
| Q-12 | PE | How to treat legacy Conduit follow / “Your Feed” chrome vs INIT-002 CAP/REQ surfaces (module naming, tab labels, Profile follow button) | PE | no | technical review | Satisfy REQs regardless of module layout; do not expand prove to non-goals (counts, comments, ∩tag) | open | Feasibility / technical review |
| Q-13 | PE | How INIT-002 prove binds API root (`src/agent.js` demo host vs overlay `http://localhost:3100/api`) | PE | no | technical review / plan | Local prove uses local API root per REQ-24; mechanism chosen in TDD/ADR | open | Feasibility / technical review |

## Draft check summary (D1–D12)

| Check | Status | Evidence / findings |
|-------|--------|---------------------|
| D1 Approved handoff current | PASS | H1 digest matches PRD file (`sha256:5a9b49dd…`); H3 map_revision=1; H2 web scope digest `sha256:63d4c2c7…`; G1 APPROVED review `commit_id` = meta PR #8 head `ee9fe45a…`; label `impact-map-lgtm`; repo affected not deferred/blocked |
| D1b No open blocking IM-* | PASS | Impact-map §10: no `Blocking: yes` open rows; IM-01..IM-05 and IM-08..IM-09 defaults applied into REQs / out-of-scope |
| D2 Complete PRD traceability | PASS | CAP-01..CAP-04 each have ≥1 REQ; every REQ cites PRD CAP/REQ or OQ/IM default / impact-map web scope |
| D2b Journeys carried | PASS | J-01..J-16 carried; live evidence cites CAP/J |
| D3 Repo-bounded scope | PASS | Consumer UAT scope matches impact-map H2; api/local and non-goals listed under Out of scope |
| D4 Observable acceptance | PASS | Each REQ has condition/event, observable result, evidence layer; implementation-neutral |
| D5 Negative/failure paths | PASS | Table covers empty/denied, unpersonalized churn, stream privacy, lifecycle, missing/self/unseated |
| D6 Assumptions/questions | PASS | Assumptions evidenced; questions have lane/owner/blocking/default/status; material PM defaults applied into REQs |
| D7 Cross-repository contracts | PASS | CTR-01..CTR-04 semantic seams with provider=api, consumer=web |
| D8 NFR applicability | PASS | All eight areas specified or N/A with reason |
| D9 As-built alignment | PASS | As-built index absent; source scan notes legacy Conduit follow/feed chrome under `src/` vs INIT-002 CAP/REQ obligations (new prove surface); overlay already rewrites API root for local prove |
| D10 Dependency order | PASS | Matches impact-map: api → web → local; web depends on api |
| D11 Zero unresolved blockers | PASS | No blocking Q remaining; Q-06/Q-07/Q-10/Q-11/Q-12/Q-13 non-blocking with defaults |
| D12 Output completeness | PASS | Header H1–H3/G1, tables, check summary, outcome, PR readiness, handoff present |

**Draft verdict:** PASS

**Selected workflow outcome:** `pass`
**Outcome reason:** D1–D12 PASS, Gate 1 current, zero unresolved material questions, IM defaults locked into REQs, PR READY for Forge publish.

Do not advance to `/initiative-feasibility` unless the workflow outcome is
`pass`, the draft verdict is PASS, and the developer review below is complete.

## PR readiness handoff

| Item | Value |
|------|-------|
| Workflow outcome | `pass` — D1–D12 PASS; Gate 1 current; material IM defaults applied |
| Verdict | PR READY |
| Existing spec PR | none |
| Proposed branch | `chore/INIT-REALWORLD-002-spec-realworld-web` |
| Proposed base | `develop` |
| Proposed title | `[INIT-REALWORLD-002] Spec — realworld-web` |
| PR type | **Draft** (entire spec lifecycle) |
| Local artifacts to publish | `docs/specification/product/INIT-REALWORLD-002-web.md`, `docs/specification/README.md`, `docs/specification/reports/Spec-PR-Body-INIT-REALWORLD-002-realworld-web.md` |
| Forge readiness | fill `handoff.forge` for `open_draft_pr`; recommend `/commit-workspace` then `/open-draft-pr` — do not commit/push/open PR inside this skill |
| Reviewer | @drivestream-lab/frontend-devs |
| Initial Gate 2 label | `spec-pending` |
| Additional invalidation label | none |
| Blocking items | none |

**No GitHub side effects have occurred.** Persist the draft locally; Forge publish is a later `/commit-workspace` / `/open-draft-pr` or Gateflow ForgeClient step.

### Proposed Draft PR body

```markdown
## Initiative

INIT-REALWORLD-002 — Author-subscription trust with denial-honest empties

## Meta handoff

- Meta PRD PR: https://github.com/drivestream-lab/realworld-meta/pull/8
- Approved meta head: `ee9fe45ad23d988eface7517a3a65a7b58b592c1`
- Impact-map revision: 1
- PRD digest: `sha256:5a9b49dd102202262edca3cb3d2bf315f43782884372d945be9274124b974c92`
- Repo scope digest: `sha256:63d4c2c76934a2659455417bb02885bcc61843a4c5d8b517bc5e966631f1e566`

## Spec path

`docs/specification/product/INIT-REALWORLD-002-web.md`

## Summary

- 24 REQs covering CAP-01..CAP-04 as web consumer UAT surface for CTR-01..CTR-04
- Author-subscription + denial-honest empty kill-path prove on web against local API; api/local out of scope
- Open non-blocking engineering questions: Q-06, Q-07, Q-10, Q-11, Q-12, Q-13

## Gate 2 — spec package readiness

Initial label: `spec-pending`

- [ ] Spec slice published on this PR head (via Forge `/commit-workspace` / `/open-draft-pr`)
- [ ] Feasibility report (later Forge publish)
- [ ] Technical design + ADRs (later Forge publish)
- [ ] Implementation plan §9 (later Forge publish)
- [ ] PE sets `spec-lgtm` on exact final head before merge

Requested reviewer: @drivestream-lab/frontend-devs
```

## Developer review

- [ ] Scope matches the approved impact-map repo scope digest
- [ ] REQs have condition/event, observable result, and evidence layer
- [ ] Contracts are semantic (logical operation); no architecture decisions in REQs
- [ ] No blocking question remains
- [ ] Developer confirmed draft is ready for feasibility

## After Draft PR creation

PE controls Gate 2 labels on the spec PR. Never infer approval from labels
alone — `spec-lgtm` requires matching artifacts on the exact PR head.

Provision labels before PR creation when missing:

```bash
launchpad apply-gates --repo realworld-web --apply
```

| PE action | Remove | Add |
|-----------|--------|-----|
| Pending/new revision | `spec-lgtm`, `spec-blocked` | `spec-pending` |
| Request changes/hold | `spec-pending`, `spec-lgtm` | `spec-blocked` |
| Approve full package | `spec-pending`, `spec-blocked`, `spec-revised`, `spec-stale` | `spec-lgtm` |

## References

- PRD: `realworld-meta/prd/INIT-REALWORLD-002.md`
- Meta PRD PR: https://github.com/drivestream-lab/realworld-meta/pull/8
- Spec PR: pending
- Service profile: not present (`docs/specification/product/00-service-profile.md` absent)
- Impact map: `realworld-meta/prd/reports/Impact-Map-INIT-REALWORLD-002.md`

```yaml
handoff:
  contract: sdd-delivery/v2
  stage: spec-draft
  outcome: pass
  artifact:
    path: docs/specification/product/INIT-REALWORLD-002-web.md
  blockers: []
  signals:
    pr_ready: true
    draft_verdict: PASS
    map_revision: 1
    meta_pr: https://github.com/drivestream-lab/realworld-meta/pull/8
    meta_pr_approved_head: ee9fe45ad23d988eface7517a3a65a7b58b592c1
    prd_digest: sha256:5a9b49dd102202262edca3cb3d2bf315f43782884372d945be9274124b974c92
    scope_digest: sha256:63d4c2c76934a2659455417bb02885bcc61843a4c5d8b517bc5e966631f1e566
    codegraph_provider: degraded-none
    grounding_depth: light
    open_non_blocking_questions:
      - Q-06
      - Q-07
      - Q-10
      - Q-11
      - Q-12
      - Q-13
  next_candidates:
    - spec-pr-action
  human_checkpoint: false
  external_action: true
  forge:
    action: open_draft_pr
    draft: true
    apply_labels:
      - spec-pending
    title: "[INIT-REALWORLD-002] Spec — realworld-web"
    body_path: docs/specification/reports/Spec-PR-Body-INIT-REALWORLD-002-realworld-web.md
```

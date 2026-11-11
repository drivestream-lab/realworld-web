# INIT-REALWORLD-001 — spec slice for realworld-web

| Field | Value |
|-------|-------|
| Initiative | INIT-REALWORLD-001 |
| PRD | `realworld-meta/prd/INIT-REALWORLD-001.md` |
| PRD digest (H1) | `sha256:cdc1f66d5cf4c1f12fd0f9ed8bf28d320d4aef941d6abbb0612f53886a1fcfb4` |
| Meta PR | https://github.com/drivestream-lab/realworld-meta/pull/5 |
| Meta PR approved head (G1) | `a500df8eeefbea095ecdd65e00edb128416c3cf4` |
| Impact map | `realworld-meta/prd/reports/Impact-Map-INIT-REALWORLD-001.md` |
| Impact-map revision (H3) | `1` |
| Repo scope digest (H2) | `sha256:0124a41f5c7c3acba48e93564e5cc8cb84f218d6f7b863c27a98bfc3fcfb7549` |
| Tech-lead approval | https://github.com/drivestream-lab/realworld-meta/pull/5#pullrequestreview-5835509709 — `adh-reviewer[bot]` (adh.pe Gate-1 attestation), APPROVED, submitted `2026-11-10T01:45:11Z`, `commit_id` = approved head; label projection `impact-map-lgtm`; PR merged `2026-11-10T02:01:17Z` |
| Repo | `drivestream-lab/realworld-web` |
| Date | 2026-11-11 |
| Status | Draft — dev review required before Forge publish |

> **H4 citations:** The H1–H3 (and G1) rows above are the durable authority
> carrier for mid-lane freshness. Feas / TDD / plan digests are walk-time only
> and may be purged at initiative closure — see
> `prayog-skills/references/artifact-write-contract.md`.

## Overview

`realworld-web` is the **consumer UAT surface** for Wave0a’s public bulletin and
session-bound contribution seat: ungated guest browse of feed / articles / tag
lanes / public profiles; register → login seat → logout unseat → reseat;
seated tagged markdown publish and own-article revise/delete; private favorites
shelf; own nameplate self-update — driving the three kill paths against
`realworld-api` in `realworld-local` (PRD CAP-01..CAP-06, CTR-01..CTR-06,
Lock 9).

**Out of scope for this repo:** API provider behavior and persistence
(`realworld-api`); compose environment wiring (`realworld-local`); comments,
follow graphs, personalized feeds, OAuth, email verification, password recovery,
account deletion, admin/moderation, public who-favorited social proof, and
server-side draft queues.

**Ownership:** this slice states observable client/UAT behavior, acceptance,
field meaning shown to actors, fail-closed denials, and consumer contract
expectations. Architecture / routing / state-management realization are
deferred to `/initiative-feasibility` and `/spec-technical-review` (see Spec
questions Q-10..Q-12).

**As-built baseline:** a Conduit-shaped React + Redux SPA already exists under
`src/` (home/feed, article, editor, auth, settings, profile, favorites, plus
out-of-INIT comments / follow / “Your Feed”). Wave0a work **aligns and
constrains** that surface to the REQs below (local API root, ≥1-tag
publish/edit refuse, private-shelf isolation, no who-favorited social proof,
OQ-03 identity-locked nameplate fields, OQ-04 login-required seat for
contribution kill path) — it is not a greenfield empty client.

## Functional requirements

| ID | Requirement | PRD source | Condition / event | Observable result | Evidence layer |
|----|-------------|-----------|-------------------|-------------------|----------------|
| REQ-01 | Present the global feed without requiring a contribution seat | PRD REQ-01 / CAP-01 | Guest opens the bulletin home | Guest sees the published-article listing state (populated or empty) with no account wall | live (CAP-01, J-01) |
| REQ-02 | Open a published article without a contribution seat | PRD REQ-02 / CAP-01 | Guest selects a published article when at least one exists | Full published content is readable while still unseated | live (CAP-01, J-01) |
| REQ-03 | Browse a tag lane without a contribution seat | PRD REQ-03 / CAP-01 | Guest selects a tag | Guest sees only published articles that currently carry that tag (or an empty lane for that tag) | live (CAP-01, J-02) |
| REQ-04 | View a public profile without a contribution seat | PRD REQ-04 / CAP-01 | Guest opens an existing member’s public profile | Public presentation is visible; that member’s published articles are discoverable from it (list may be empty) | live (CAP-01, J-03) |
| REQ-05 | Allow tag-hop discovery from a published article | PRD REQ-05 / CAP-01 | Guest reading an article follows one of its tags into a tag lane | Guest lands on the lane for that tag and can open another matching article when one exists | live (CAP-01, J-04) |
| REQ-06 | Show a multi-tagged article in every matching lane | PRD REQ-06 / CAP-01 | A published article carries ≥2 tags; Guest opens each of those tag lanes | The same article appears in each lane for its current tags | live (CAP-01, J-04) |
| REQ-07 | Keep bulletin reading available after the actor unseats | PRD REQ-07 / CAP-01 | Member logs out then opens the global feed and a published article (including one they authored while seated) | Feed and article remain readable; no contribution seat is required | live (CAP-01, J-17) |
| REQ-08 | Register a new membership (eligibility) with unique username, unique email, and password | PRD REQ-08 / CAP-02; OQ-01/OQ-04 defaults | Visitor submits registration with unique username + unique email + password | Membership eligibility exists; actor can obtain a seat via **login**; registration alone is **not** the kill-path seated contribution session | live (CAP-02, J-05) |
| REQ-09 | Refuse registration that collides with an existing membership identity | PRD REQ-09 / CAP-02 | Visitor registers with a taken username or taken email | No second membership; actor is told registration did not succeed; no seat granted from that attempt | unit / live (CAP-02, J-05) |
| REQ-10 | Seat an existing member by authenticating | PRD REQ-10 / CAP-02 | Registered member submits valid credentials on login | Authenticated session exists; contribution seat is active | live (CAP-02, J-06) |
| REQ-11 | Refuse seating with invalid credentials | PRD REQ-11 / CAP-02 | Actor submits credentials that do not match a membership | No authenticated session; contribution seat inactive | unit / live (CAP-02, J-11) |
| REQ-12 | Unseat on logout and revoke contribution actions | PRD REQ-12 / CAP-02; OQ-05 default | Authenticated member logs out (this session) | Publish, favorite, and profile self-update are unavailable until a later successful login; bulletin reading remains available. Wave0a proves unseat on the logging-out session only | live (CAP-02, J-07) |
| REQ-13 | Reseat after re-login | PRD REQ-13 / CAP-02 | Previously logged-out member authenticates again with valid credentials | Publish, favorite, and profile self-update are available again | live (CAP-02, J-07) |
| REQ-14 | Deny contribution actions while unseated at the product rule layer | PRD REQ-14 / CAP-02 | Guest or post-logout actor attempts publish, favorite/unfavorite, and profile self-update | Each attempt fails closed; no new article, no shelf change, no profile change (product rule, not merely hidden chrome) | live (CAP-02, J-08) |
| REQ-15 | Publish a tagged markdown article into the bulletin with immediate visibility | PRD REQ-15 / CAP-03; OQ-02 default | Seated member publishes with markdown body and ≥1 non-blank tag | Article is immediately visible in the global feed and each of its tag lanes; Guests can open it; it appears among the author’s public-profile articles | live (CAP-03, J-06) |
| REQ-16 | Refuse publish when zero tags (or only blank tags) are provided | PRD REQ-16 / CAP-03; OQ-02 default | Seated member attempts to publish with no non-blank tags | No new published article appears in the bulletin | unit / live (CAP-03, J-06) |
| REQ-17 | Bootstrap an empty bulletin via first seated publish | PRD REQ-17 / CAP-03 | Bulletin has zero published articles; a seated member then publishes a tagged article | Guest global feed becomes non-empty and Guest can open that article | live (CAP-03, J-12) |
| REQ-18 | Refuse publish when the actor is unseated | PRD REQ-18 / CAP-03 | Guest or post-logout actor attempts to publish | No new published article is created | live (CAP-03, J-08) |
| REQ-19 | Leave the bulletin unchanged when compose is abandoned or interrupted by unseat | PRD REQ-19 / CAP-03 | Seated member starts composing but logs out or abandons without a successful publish | No new published article appears; client-only scratch must not publish | live (CAP-03, J-18) |
| REQ-20 | Edit only own published articles | PRD REQ-20 / CAP-04 | Author changes title, body, and/or tags on an article they own | Subsequent reads show the updated published content; visibility remains immediate | live (CAP-04, J-09) |
| REQ-21 | Keep tag lanes consistent after tag-set edit | PRD REQ-21 / CAP-04 | Author changes an article’s tags | Article appears in lanes for its new tags and no longer appears in lanes for removed tags | live (CAP-04, J-15) |
| REQ-22 | Refuse an edit that would leave zero non-blank tags | PRD REQ-22 / CAP-04; OQ-02 default | Author attempts to save an update with an empty / blank-only tag set | Article’s prior published content and tag lanes remain unchanged | unit / live (CAP-04, J-15) |
| REQ-23 | Delete only own published articles | PRD REQ-23 / CAP-04; OQ-08 default | Author deletes an article they own | Article disappears from global feed, tag lanes, and author public-profile article list; Guest open-by-reference shows stable not-found (no prior body) | live (CAP-04, J-09) |
| REQ-24 | Refuse edit or delete of another author’s article | PRD REQ-24 / CAP-04 | Peer member attempts to change or remove an article they do not own | That article’s content and bulletin presence are unchanged; denial is observed in the client | live (CAP-04, J-10) |
| REQ-25 | Favorite another author’s article onto a private shelf | PRD REQ-25 / CAP-05 | Seated member favorites an article they do not own | Article appears on that member’s private shelf; client does **not** present public who-favorited social proof (no actor lists / counts as social proof) | live (CAP-05, J-13) |
| REQ-26 | Unfavorite a previously favorited article | PRD REQ-26 / CAP-05 | Seated member removes a favorite | Article no longer appears on that member’s private shelf | live (CAP-05, J-13) |
| REQ-27 | Keep one member’s private shelf invisible to another member | PRD REQ-27 / CAP-05 | Member A has favorited an article; Member B is seated and is not A | Member B cannot inspect A’s private shelf | live (CAP-05, J-16) |
| REQ-28 | Refuse favorite of own article | PRD REQ-28 / CAP-05 | Seated member attempts to favorite an article they authored | Private shelf unchanged for that attempt | unit / live (CAP-05, J-13) |
| REQ-29 | Deny favorite actions after unseat | PRD REQ-29 / CAP-05 | Post-logout actor attempts favorite/unfavorite | Private shelf remains unchanged | live (CAP-05, J-07) |
| REQ-30 | Update own nameplate editable public fields (bio, image); keep username and email identity-locked | PRD REQ-30 / CAP-06; OQ-03 default | Seated member changes bio and/or image | Subsequent Guest (and Member) public-profile views show the updated editable public fields; username/email unchanged by self-update | live (CAP-06, J-13) |
| REQ-31 | Refuse update of another member’s nameplate | PRD REQ-31 / CAP-06 | Peer member attempts to change Member A’s profile | A’s public profile remains unchanged | live (CAP-06, J-19) |
| REQ-32 | Deny nameplate self-update after unseat | PRD REQ-32 / CAP-06 | Post-logout actor attempts profile self-update | Profile remains unchanged | live (CAP-06, J-07) |
| REQ-33 | Keep a private-shelf pointer from resurrecting a deleted article’s public readability | PRD REQ-33 / CAP-05; OQ-07/OQ-08 defaults | Author deletes a published article that still has (or previously had) a favorite/shelf pointer | Private shelf must not expose the deleted article’s published body; Guest open-by-reference remains stable not-found; favorite of missing/deleted article fails closed with shelf unchanged | live (CAP-05, J-09) |
| REQ-34 | Present the global feed newest-first by publish/update time | PRD OQ-06 default / CAP-01 | Guest or member opens the global feed with ≥2 articles | Articles appear newest-first consistent with provider ordering | live (CAP-01, J-01) |
| REQ-35 | Abandon registration mid-flow leaves actor unseated | PRD §7 J-05 abandon / CAP-02 | Visitor abandons registration without successful create | Remains Guest; no partial membership seat | live (CAP-02, J-05) |
| REQ-36 | Drive Wave0a kill-path live prove against the local API in compose | PRD Lock 9 / CAP-01..CAP-03 | UAT observer runs guest bulletin read, seated auth+publish, and post-logout seat denial on web | Three kill paths are demonstrable on `realworld-web` against `realworld-api` via `realworld-local` | live (CAP-01, CAP-02, CAP-03; J-01, J-06, J-07) |

## Capabilities (carried from PRD)

| ID | Capability | Journeys | Notes |
|----|------------|----------|-------|
| CAP-01 | Public bulletin reading | J-01, J-02, J-03, J-04, J-12, J-14, J-17 | Consumer UAT; never requires a seat |
| CAP-02 | Contribution seat lifecycle | J-05, J-06, J-07, J-08, J-11, J-14, J-17, J-18 | Register eligibility, seat, unseat, reseat, denial |
| CAP-03 | Seated bulletin contribution | J-06, J-08, J-12, J-14, J-18 | Tagged publish; immediate visibility; no draft |
| CAP-04 | Seated own-work revision | J-09, J-10, J-15 | Edit/delete own; tag lanes; peer denial |
| CAP-05 | Private shelf | J-07, J-08, J-09, J-13, J-16 | Favorites; isolation; no social proof; no resurrection |
| CAP-06 | Own nameplate | J-07, J-08, J-13, J-19 | Self-update bio/image; peer denial |

## Journeys (carried from PRD)

| ID | Actor | Trigger | Main flow | Primary edge | Abandon | Caps |
|----|-------|---------|-----------|--------------|---------|------|
| J-01 | Guest | Wants published content | Open global feed → open article when present → read | Empty bulletin → empty feed, still ungated | Leaves without registering | CAP-01 |
| J-02 | Guest | Follows a topic | Open tag lane → scan → open matching article | Empty tag → empty for that tag only | Leaves lane | CAP-01 |
| J-03 | Guest | Curious about an author | Open public profile → see public fields → open author article when present | Zero articles → profile visible, empty list | Leaves profile | CAP-01 |
| J-04 | Guest | Continues discovery across tags | Read multi-tagged article → follow each tag → confirm membership → open another match when present | Sole article in a lane → only that article | Stops after one hop | CAP-01 |
| J-05 | Visitor → eligible member | Decides to join | Register unique identity → membership exists → login (OQ-04) | Duplicate identity → refused; remains Guest | Abandons form; remains Guest | CAP-02 |
| J-06 | Seated member | Puts writing on bulletin | Authenticate → compose tagged markdown → publish → Guest sees feed/lanes/profile | Zero tags → publish refused | Logout before publish; no article | CAP-02, CAP-03 |
| J-07 | Seated → Guest → Seated | Ends then resumes seat | Logout → contribution denied → reads still work → login → contribution works | Stale seat must not mutate while unseated | N/A | CAP-02, CAP-05, CAP-06 |
| J-08 | Guest | Probes the seat | Attempts publish, favorite, profile self-update while unseated | Each fails closed, no side effects | Registers (enters J-05) | CAP-02, CAP-03, CAP-05, CAP-06 |
| J-09 | Author | Fixes or removes own writing | Edit and/or delete own article → bulletin reflects | After delete, not-found; shelf must not resurrect body (REQ-33) | Cancels edit; prior version remains | CAP-04, CAP-05 |
| J-10 | Peer member | Attacks ownership | Attempts edit/delete on another’s article | Bulletin unchanged; denial observed | Returns to own articles | CAP-04 |
| J-11 | Member | Valid vs invalid seating | Login succeeds; bad credentials refused | Invalid login leaves seat inactive | Stops trying | CAP-02 |
| J-12 | Seated + Guest | Bootstraps bulletin | From zero articles, publish first tagged → Guest feed non-empty → Guest opens | Publish fail → empty ungated feed remains | Abandon compose; stays empty | CAP-01, CAP-03 |
| J-13 | Seated member | Private shelf + nameplate | Favorite other author’s article → see shelf → unfavorite; update bio/image → Guest sees update | Favorite own → refused | Leaves shelf unused; cancels nameplate edit | CAP-05, CAP-06 |
| J-14 | Guest → Seated | Converts mid-read | Guest reads → registers → logs in → publishes own tagged article | If login skipped, contribution unavailable until seated | Abandons join; still can read | CAP-01, CAP-02, CAP-03 |
| J-15 | Author | Retags published writing | Edit tag set → lane membership moves; empty tag set refused | Reader in old lane no longer finds article | Cancels edit; prior tags remain | CAP-04 |
| J-16 | Peer member | Probes shelf privacy | A favorites; B attempts to inspect A’s shelf | B cannot see A’s favorites | B favorites same article into B’s own shelf | CAP-05 |
| J-17 | Former seated (now Guest) | Confirms bulletin open after unseat | Logout → open feed → open own prior article → read as Guest | Contribution still denied on same visit | Leaves without reseating | CAP-01, CAP-02 |
| J-18 | Seated member | Abandons / unseats mid-compose | Starts compose → logout or abandon without publish → Guest feed unchanged | Partial client state must not create published article | N/A | CAP-02, CAP-03 |
| J-19 | Peer member | Attacks nameplate ownership | Attempts to change another’s public profile | Target unchanged; denial observed | Returns to own nameplate | CAP-06 |

> Wave0a live prove of journeys is **driven by this repo** against `realworld-api`
> in `realworld-local` (PRD Lock 9). Provider observables remain owned by the API
> slice; this repo must not drop in-scope `J-*`.

## Negative and failure paths

| REQ | Condition | Required behavior | Why it matters | Evidence |
|-----|-----------|-------------------|----------------|----------|
| REQ-01 | Zero published articles | Empty feed; still no account wall | Open bulletin ≠ must have content | live J-01 |
| REQ-03 | Tag with no articles | Empty lane for that tag only | Prevents false discovery | live J-02 |
| REQ-07 | After logout, read own prior article | Readable as Guest; contribution denied | Separates bulletin openness from seat | live J-17 |
| REQ-09 | Duplicate username or email | Fail closed; no second membership; no seat | Eligibility integrity | unit / live J-05 |
| REQ-11 | Invalid credentials | No seat | Protects seat gate | unit / live J-11 |
| REQ-12 | After logout | Contribution unavailable; reading works | Kill assumption — unseat | live J-07 |
| REQ-14 / REQ-18 | Unseated contribution cluster | Fail closed; no side effects | Kill assumption — product rule not chrome | live J-08 |
| REQ-16 / REQ-22 | Zero-tag create or edit | No create; no destructive empty-tag save | Tagged-article lock | unit / live |
| REQ-19 | Abandon / unseat mid-compose | Bulletin unchanged; no published draft | No-draft lock | live J-18 |
| REQ-24 | Cross-author mutate | Bulletin unchanged; denial observed | Ownership integrity | live J-10 |
| REQ-27 | Peer views another’s shelf | Isolation holds | Privacy beyond “no public proof” | live J-16 |
| REQ-28 | Favorite own article | Refused; shelf unchanged | Lock 10 | unit / live J-13 |
| REQ-29 / REQ-32 | Post-logout shelf/nameplate | No mutation | Kill assumption — unseat | live J-07 |
| REQ-31 | Peer nameplate attack | Target unchanged | Nameplate ownership | live J-19 |
| REQ-33 / OQ-07 / OQ-08 | Shelf pointer or open after delete; favorite missing/deleted | No public body; stable not-found; shelf unchanged on failed favorite | Prevents ghost public content | live J-09 |
| REQ-35 / J-05 | Abandon registration | Remains Guest; no partial seat | Avoid half-open seat | live J-05 |
| J-14 | Guest mid-read then seats | Reading remains open; publish only after seated | Bulletin stays open across seat change | live J-14 |

## Out of scope for this repo

- API provider contracts, persistence, and fail-closed authority — **`realworld-api`**
- Local compose / environment wiring for continuous UAT — **`realworld-local`**
- Comments, follow/unfollow, personalized (“Your Feed”) timeline — PRD non-goals (legacy code may still exist; not Wave0a prove)
- OAuth / third-party identity, email verification, password recovery, account deletion, admin/moderation
- Public who-favorited social proof (counts or actor lists as social proof)
- Server-side draft or moderation queues
- Multi-session logout cascade beyond the logging-out session (OQ-05 deferred)
- Live mid-read push after remote delete (OQ-09 deferred — next navigation / reopen sufficient)
- API-only prove without this web UAT surface (PRD Lock 9)

## Cross-service contracts

| Contract ID | Provider / owner | Consumer / owner | Entry point | Input shape | Output shape | Invariants | Errors | Compatibility / versioning | Contract-test location |
|-------------|------------------|------------------|-------------|-------------|--------------|------------|--------|----------------------------|------------------------|
| CTR-01 | realworld-api / @drivestream-lab/backend-devs | realworld-web / @drivestream-lab/frontend-devs | Read the public bulletin | Optional tag filter; article identity for open; profile identity for public read | Global listing, tag-filtered listing, single published article, public profile (+ author’s published articles); newest-first global ordering (REQ-34) | Only published articles; Guests may read; multi-tag membership; no draft states; reading never requires a seat | Missing article → stable not-found; empty sets allowed | Wave0a additive Conduit-shaped consumer; breaking field meaning requires INIT change | Planned: `tests/verify/` live packs (exact paths in plan) |
| CTR-02 | realworld-api / backend-devs | realworld-web / frontend-devs | Manage the contribution seat | Register: unique username + unique email + password; authenticate: credentials; logout: end this session’s seat | Eligibility on register; seated session on login; unseated after logout; reseat on later login | Contribution mutations require valid seat; reading never requires seat; register ≠ kill-path seat (OQ-04) | Duplicate identity; invalid credentials; malformed required identity fields; absent session → treat as unseated | Same as CTR-01 | Planned verify + unit auth denials |
| CTR-03 | realworld-api / backend-devs | realworld-web / frontend-devs | Contribute to the bulletin | Create own markdown article with ≥1 non-blank tag | Immediately visible published article in global + tag lanes + author profile | Seat required; zero/blank tags rejected; abandon/unseat without success creates nothing | Unauthenticated; zero tags | Same | Planned verify J-06/J-12/J-18 |
| CTR-04 | realworld-api / backend-devs | realworld-web / frontend-devs | Revise own bulletin work | Update/delete own articles; tag-set on update when tags supplied | Updated or removed published presence; tag lane membership moves | Ownership required; zero tags rejected; peer mutate denied | Unauthenticated; not owner; zero tags; missing article | Same | Planned verify J-09/J-10/J-15 |
| CTR-05 | realworld-api / backend-devs | realworld-web / frontend-devs | Maintain private shelf | Favorite/unfavorite others’ articles | Private per-member shelf membership | Shelf private + isolated across members; no who-favorited social proof; unseated denied; own-article favorite denied; no resurrection of deleted body | Unauthenticated; own article; missing/deleted article; peer inspection denied | Same | Planned verify J-13/J-16 |
| CTR-06 | realworld-api / backend-devs | realworld-web / frontend-devs | Maintain own nameplate | Self-update bio/image; public read of profile | Updated public bio/image on subsequent reads; username/email identity-locked | Only owner may update; unseated denied; peer update denied | Unauthenticated; not owner | Same | Planned verify J-13/J-19 |

## Non-functional requirements

| Area | Requirement or N/A rationale | Acceptance / evidence |
|------|------------------------------|-----------------------|
| Security | Contribution mutations (publish / favorite / profile self-update) fail closed without a valid seat (REQ-12, REQ-14, REQ-18, REQ-29, REQ-32). Credential verification fail-closed (REQ-11). Peer article and nameplate mutation denied (REQ-24, REQ-31). No OAuth in this INIT. | live J-07/J-08/J-10/J-19; unit denials where practical |
| Reliability | N/A beyond fail-closed absence of success when an action does not complete — not a multi-region SLA INIT. Mid-action network/timeout recovery is out of Wave0a UAT prove. | N/A (documented) |
| Performance / capacity | N/A for Wave0a UAT scale; no concurrent-load target locked. | N/A (documented) |
| Observability | N/A as product REQ — client logging/metrics are engineering choice, not a kill criterion. | N/A (documented) |
| Privacy / data handling | Private shelves isolated across members (REQ-25, REQ-27). No public who-favorited proof. Shelf must not resurrect deleted articles (REQ-33). Nameplate exposes only public fields (OQ-03: bio/image public; username public identity; email not a Guest bulletin requirement). | live J-13/J-16 |
| Migration / compatibility | Clean-slate UAT corpus — no prior tenant migration required. Legacy Conduit surfaces outside CAP-01..06 are not Wave0a prove targets. Client must target local API for Wave0a prove (REQ-36). | inspection / as-built |
| Rollback / recovery | N/A as product REQ — delivery rollback is engineering. | N/A (documented) |
| Operations / support | Live prove environment is `realworld-local` compose joining this web client with `realworld-api`; no 24×7 support envelope locked. | compose prove (other repo) + REQ-36 |

## Assumptions

| ID | Assumption | Evidence | Owner | Status | Invalidated when |
|----|------------|----------|-------|--------|------------------|
| A-01 | Registration uniqueness covers both username and email | PRD A-01 / OQ-01 default | PM | confirmed (locked default) | OQ-01 reopened with different uniqueness set |
| A-02 | Markdown body is the authored format; readable published content is the kill bar | PRD A-02 | PM | confirmed | Rich media becomes in-scope |
| A-03 | Author delete removes public presence; shelf must not resurrect readability | PRD A-03 / REQ-33 | PM | confirmed | Product allows public tombstone body |
| A-04 | Explicit logout defines unseat for Wave0a; idle-timeout not required | PRD A-04 / OQ-05 | PM | confirmed | Idle timeout becomes kill criterion |
| A-05 | Tags are author-chosen free-form labels; blank tags rejected; ≥1 non-blank required | PRD A-05 / OQ-02 | PM | confirmed | Curated taxonomy introduced |
| A-06 | Reseat via re-login is in scope | PRD A-06 / Lock 13 | PM | confirmed | Reseat removed from kill path |
| A-07 | Inter-member shelf isolation is required (not only absence of public proof) | PRD A-07 / Lock 16 | PM | confirmed | Privacy redefined to social-proof-only |
| A-08 | Unfinished compose has no durable published artifact; client scratch must vanish without publishing on abandon/unseat | PRD A-08 / Lock 18 | PM | confirmed | Server drafts become in-scope |
| A-09 | Global feed order is newest-first by publish/update time (client presents provider order) | PRD OQ-06 default | PM | confirmed | Different ordering locked |
| A-10 | Existing Conduit-shaped `src/` SPA is the starting codebase; Wave0a aligns behavior to REQs rather than assuming empty repo | Source scan + as-built index | PE | confirmed | Clean-room rewrite mandated without reuse |
| A-11 | Wave0a live prove uses local API (`http://localhost:3100/api`) via `realworld-local`, not the legacy production demo API root | Workspace rule + PRD Lock 9 | PE | confirmed | Different prove host locked |

## Spec questions (ambiguities — need PM or domain confirmation before feasibility)

| ID | Lane | Question | Owner | Blocking | Required by | Default if deferred | Status | Resolution link |
|----|------|----------|-------|----------|-------------|---------------------|--------|-----------------|
| Q-01 | PM | Registration field/uniqueness set (OQ-01 / IM-01) | PM | no | feasibility | Unique username + unique email + password; bio/image via nameplate | resolved | PRD OQ-01 → REQ-08/REQ-09 |
| Q-02 | PM | Tag normalization limits (OQ-02 / IM-02) | PM | no | feasibility | Free-form; reject blank; ≥1 non-blank | resolved | PRD OQ-02 → REQ-15/16/21/22 |
| Q-03 | PM | Nameplate editable vs identity-locked (OQ-03 / IM-03) | PM | no | feasibility | Bio/image editable; username/email locked | resolved | PRD OQ-03 → REQ-30 |
| Q-04 | PM | Register-may-seat vs login-required (OQ-04 / IM-04) | PM | no | feasibility | Eligibility only; login before contribution kill path | resolved | PRD OQ-04 → REQ-08/REQ-10; note as-built REGISTER auto-JWT gap |
| Q-05 | PE | Multi-session logout cascade (OQ-05 / IM-05) | PE | no | later | Unseat logging-out session only | open | PRD OQ-05; revisit if multi-device required |
| Q-06 | PM | Global feed ordering (OQ-06 / IM-06) | PM | no | feasibility | Newest-first by publish/update time | resolved | PRD OQ-06 → REQ-34 |
| Q-07 | PM | Favorite missing/deleted article (OQ-07 / IM-07) | PM | no | feasibility | Fail closed; shelf unchanged | resolved | PRD OQ-07 → REQ-33 |
| Q-08 | PM | Guest open-by-reference after delete (OQ-08 / IM-08) | PM | no | feasibility | Stable not-found; no prior body | resolved | PRD OQ-08 → REQ-23/REQ-33 |
| Q-09 | PE | Mid-read refresh vs next navigation after delete (OQ-09 / IM-09) | PE | no | later | Next navigation / reopen sufficient | open | PRD OQ-09; web-primary |
| Q-10 | PE | How client stores/clears the seat credential on login/logout (storage mechanism) | PE | no | technical review | Product rule: unseat ⇒ mutations fail closed; mechanism TBD in TDD/ADR | open | Architecture — not decided here |
| Q-11 | PE | Whether legacy “Your Feed” / comments / follow UI remain mounted but unproven vs removed/hidden for Wave0a | PE | no | technical review / plan | Remain unproven / out of Wave0a acceptance; no REQ expands them | open | Architecture / cleanup scope |
| Q-12 | PE | Whether Wave0a keeps CRA/React-Redux chassis or migrates toward harness Next.js-shaped layout | PE | no | technical review | Observable REQs are layout-neutral; choose in TDD — workspace rule: this repo is **not** Next.js | open | Architecture — not decided here |

## Draft check summary (D1–D12)

| Check | Status | Evidence / findings |
|-------|--------|---------------------|
| D1 Approved handoff current | PASS | Hermes host `/spec-draft` 2026-11-11 (non-interactive): H1 `sha256:cdc1f66d5cf4c1f12fd0f9ed8bf28d320d4aef941d6abbb0612f53886a1fcfb4` matches promoted PRD bytes; H3 map_revision=1; H2 `sha256:0124a41f5c7c3acba48e93564e5cc8cb84f218d6f7b863c27a98bfc3fcfb7549` from impact map for realworld-web; G1 APPROVED review https://github.com/drivestream-lab/realworld-meta/pull/5#pullrequestreview-5835509709 `commit_id`=`a500df8eeefbea095ecdd65e00edb128416c3cf4` equals meta PR #5 head; label `impact-map-lgtm`; merged `2026-11-10T02:01:17Z`; repo affected not deferred/blocked |
| D1b No open blocking IM-* | PASS | IM-01..IM-09 all Blocking=no; Gate 1 note allows consuming defaults |
| D2 Complete PRD traceability | PASS | CAP-01..06 each have ≥1 REQ; every REQ cites PRD REQ/CAP/OQ/Lock |
| D2b Journeys carried | PASS | J-01..J-19 carried; live evidence cites CAP/J |
| D3 Repo-bounded scope | PASS | Consumer UAT REQs; api/local called out of scope; matches H2 scope summary |
| D4 Observable acceptance | PASS | Condition/event + observable result + evidence on every REQ; no module/ADR choice in REQs |
| D5 Negative/failure paths | PASS | Table covers empty, auth, ownership, shelf, delete, abandon; each has why-it-matters |
| D6 Assumptions/questions | PASS | A-01..A-11 evidenced; Q rows have lane/owner/blocking/required-by/default/status; material PM locks resolved via PRD defaults |
| D7 Cross-repository contracts | PASS | CTR-01..06 semantic seams with provider/consumer, invariants, errors |
| D8 NFR applicability | PASS | All eight rows present; N/A rows have rationale |
| D9 As-built alignment | PASS | Overview + as-built index distinguish present legacy vs required Wave0a changes (API root, tags, shelf privacy, social proof, OQ-03/04) |
| D10 Dependency order | PASS | Matches map: api → web → local; this repo depends on realworld-api |
| D11 Zero unresolved blockers | PASS | No Blocking=yes Q/IM; Q-05/Q-09/Q-10..12 non-blocking with defaults |
| D12 Output completeness | PASS | Header H1–H3/G1, tables, checks, outcome, PR readiness, handoff envelope present |

**Draft verdict:** PASS

**Selected workflow outcome:** `pass`
**Outcome reason:** D1–D12 PASS; Gate 1 H1/H2/H3/G1 current; material OQ/IM defaults locked into REQ rows by Hermes non-interactive clarification loop; zero Blocking=yes questions; PR READY for Forge `open_draft_pr` (not executed in this skill).

Do not advance to `/initiative-feasibility` unless the workflow outcome is
`pass`, the draft verdict is PASS, and the developer review below is complete.

## PR readiness handoff

| Item | Value |
|------|-------|
| Workflow outcome | `pass` — D1–D12 PASS; Gate 1 current; material OQs locked into REQs |
| Verdict | PR READY |
| Existing spec PR | none |
| Proposed branch | `chore/INIT-REALWORLD-001-spec-realworld-web` |
| Proposed base | `develop` |
| Proposed title | `[INIT-REALWORLD-001] Spec — realworld-web` |
| PR type | **Draft** (entire spec lifecycle) |
| Local artifacts to publish | `docs/specification/product/INIT-REALWORLD-001-web.md`, `docs/specification/product/00-service-profile.md`, `docs/specification/as-built/implementation-status.md`, `docs/specification/README.md` |
| Forge readiness | `handoff.forge` filled for `open_draft_pr`; recommend `/commit-workspace` then `/open-draft-pr` (or Gateflow ForgeClient on `spec-pr-action`) — do not commit/push/open PR inside this skill |
| Reviewer | @drivestream-lab/frontend-devs |
| Initial Gate 2 label | `spec-pending` |
| Additional invalidation label | none |
| Blocking items | none |

**No GitHub side effects have occurred.** Persist the draft locally; Forge publish requires `/commit-workspace` / `/open-draft-pr` or automated `spec-pr-action`.

### Proposed Draft PR body

```markdown
## Initiative

INIT-REALWORLD-001 — Public bulletin with session-bound contribution seat

## Meta handoff

- Meta PRD PR: https://github.com/drivestream-lab/realworld-meta/pull/5
- Approved meta head: `a500df8eeefbea095ecdd65e00edb128416c3cf4`
- Impact-map revision: 1
- PRD digest: `sha256:cdc1f66d5cf4c1f12fd0f9ed8bf28d320d4aef941d6abbb0612f53886a1fcfb4`
- Repo scope digest: `sha256:0124a41f5c7c3acba48e93564e5cc8cb84f218d6f7b863c27a98bfc3fcfb7549`

## Spec path

`docs/specification/product/INIT-REALWORLD-001-web.md`

## Summary

- Consumer UAT slice for CAP-01..CAP-06 / CTR-01..CTR-06 (REQ-01..REQ-36)
- Aligns legacy Conduit `src/` SPA to Wave0a seat/bulletin rules (local API, ≥1 tag, private shelf, nameplate locks, fail-closed unseat)
- Open non-blocking engineering questions: Q-05, Q-09, Q-10, Q-11, Q-12

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

- PRD: `realworld-meta/prd/INIT-REALWORLD-001.md`
- Meta PRD PR: https://github.com/drivestream-lab/realworld-meta/pull/5
- Spec PR: pending
- Service profile: `docs/specification/product/00-service-profile.md`
- As-built: `docs/specification/as-built/implementation-status.md`
- Impact map: `realworld-meta/prd/reports/Impact-Map-INIT-REALWORLD-001.md`

```yaml
handoff:
  contract: sdd-delivery/v2
  stage: spec-draft
  outcome: pass
  artifact:
    path: docs/specification/product/INIT-REALWORLD-001-web.md
  blockers: []
  signals:
    pr_ready: true
    codegraph_provider: degraded-none
    grounding_depth: light
    map_revision: 1
    meta_pr: https://github.com/drivestream-lab/realworld-meta/pull/5
    meta_pr_approved_head: a500df8eeefbea095ecdd65e00edb128416c3cf4
    prd_digest: sha256:cdc1f66d5cf4c1f12fd0f9ed8bf28d320d4aef941d6abbb0612f53886a1fcfb4
    scope_digest: sha256:0124a41f5c7c3acba48e93564e5cc8cb84f218d6f7b863c27a98bfc3fcfb7549
  next_candidates:
    - spec-pr-action
  human_checkpoint: false
  external_action: true
  forge:
    action: open_draft_pr
    draft: true
    apply_labels:
      - spec-pending
    title: "[INIT-REALWORLD-001] Spec — realworld-web"
    body_path: docs/specification/product/INIT-REALWORLD-001-web.md
```

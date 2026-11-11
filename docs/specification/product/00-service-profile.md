# Service profile — realworld-web

| Field | Value |
|-------|-------|
| Repo | `drivestream-lab/realworld-web` |
| Role | Web **consumer UAT surface** for RealWorld Conduit-shaped bulletin + contribution seat |
| Profile | Talks to local API at `http://localhost:3100/api` (workspace rule); compose prove via `realworld-local` |
| Providers | `realworld-api` (CTR-01..CTR-06) |
| Source layout (as-found) | Pinned gothinkster React + Redux SPA under `src/` (not Next.js) — architecture realization deferred to TDD/ADR |

## Domain this repo owns

- Guest bulletin browse (global feed, article open, tag lanes, public profiles) without a seat
- Contribution seat lifecycle UI (register eligibility, login seat, logout unseat, reseat)
- Seated publish / own-article revise-delete with ≥1 tag, against the API
- Private favorites shelf presentation with inter-member isolation
- Own nameplate self-update surfaces
- Wave0a live prove of the three kill paths on web against API

## Explicit non-ownership (other repos / non-goals)

- Bulletin/seat source of truth and provider contracts — `realworld-api`
- Compose / local prove environment — `realworld-local`
- Comments, follow graphs, personalized feeds, OAuth, email verification, admin tooling — out of INIT-REALWORLD-001

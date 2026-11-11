# Implementation status (as-built index)

> Bootstrap index written during `/spec-draft` for INIT-REALWORLD-001 because no prior
> `docs/specification/` tree existed. Rows describe **current tree evidence** under
> `src/`, not Wave0a acceptance.

| Capability / surface | Status | Evidence | Notes |
|----------------------|--------|----------|-------|
| Global feed / tag filter / article open | present (legacy) | `src/components/Home/`, `Article/`, `agent.js` Articles | Guest browse exists; empty list allowed by Conduit chrome |
| Public profiles + author articles | present (legacy) | `src/components/Profile.js` | Also exposes Follow and Favorites tab (Follow out of INIT) |
| Register / login / logout | present (legacy) | `Register.js`, `Login.js`, `Settings.js`, `middleware.js` | REGISTER success stores JWT and seats — conflicts with OQ-04 Wave0a default (eligibility then login for kill path) |
| Editor create/update | present (legacy) | `src/components/Editor.js`, `reducers/editor.js` | Submits `tagList` as-is; no client refuse of zero/blank tags (gap vs REQ-16/22) |
| Article delete (author) | present (legacy) | `src/components/Article/ArticleActions.js` | Author-only chrome present |
| Favorites | present (legacy) | `ArticlePreview.js`, `ProfileFavorites.js` | Shows `favoritesCount` (social-proof non-goal); any `@user/favorites` route can load another member’s shelf (gap vs REQ-27) |
| Personalized “Your Feed” | present (legacy) | `Home/MainView.js`, `Home/index.js` | **Out of scope** for INIT-REALWORLD-001 |
| Comments | present (legacy) | `src/components/Article/Comment*` | **Out of scope** |
| Follow / unfollow | present (legacy) | `Profile.js`, `agent.js` Profile | **Out of scope** |
| API root | present (legacy) | `src/agent.js` `API_ROOT` | Points at production Conduit demo URL — Wave0a must use local API (`http://localhost:3100/api`) |
| Spec slice INIT-REALWORLD-001 | draft | `docs/specification/product/INIT-REALWORLD-001-web.md` | Not implemented / not live-verified |
| Live verify harness | absent | `tests/` | No `tests/verify` tree yet |

Per-initiative detail: none yet (`Implementation-Status-INIT-REALWORLD-001.md` after waves).

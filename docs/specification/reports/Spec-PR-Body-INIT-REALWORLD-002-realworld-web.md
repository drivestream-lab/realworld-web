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

# Working in the Workato Labs hub

This repo is the **Workato Labs hub**: a Jekyll static site (the public front door — `index.html`,
`principles.html`, `README.md`, `assets/`, `downloads/`) **and** the home of **cross-project
decision records** under `docs/adrs/`.

Two audiences, kept deliberately separate:

- The **site** is the curated front door. Anything that renders is visitor-facing.
- The **ADRs** are governance docs for contributors and agents. They are **excluded from the Jekyll
  build** (`docs` is in `_config.yml`'s `exclude`). Don't move them into the site or link them from
  site pages without a deliberate decision — keeping them repo-only is what lets them stay candid
  (see LABS-0000 §7).

## Cross-Project Decision Records (ADRs)

Architectural decisions that span **two or more** Workato Labs repos live in `docs/adrs/` (index:
`docs/adrs/README.md`). A decision internal to a single repo belongs in *that repo's* `docs/adrs/`,
not here. These are **living records** — written as hypotheses to be checked against the code, not
settled truth.

- **Verify before relying.** Treat an ADR's claims as a hypothesis to check against the current code
  in the affected repos — even one marked `Accepted`. If reality and the ADR disagree, reality wins.
- **The cross-repo amend rule.** If a change in *any* affected repo contradicts a cross-project ADR,
  amend that ADR here — in place, as a dated `> **Amendment (Month Year): …**` blockquote that
  preserves the original text — and cross-link the two changes. The amendment lands as its own
  labs-repo change. (This replaces the linter's single-repo "amend in the same PR" rule.)
- **The ADR owns its dependency map.** Each cross-project ADR lists its `Affected repos` in the
  header. That's the authoritative statement of the relationship — keep it complete. We do **not**
  rely on every affected repo carrying a back-reference; back-references are per-repo and optional,
  and we never force ADR machinery onto a repo that doesn't have it (LABS-0000 §5).
- **IDs carry the `LABS-` prefix** so a reference from another repo is never confused with that
  repo's own ADR numbers. New ADR: copy `docs/adrs/TEMPLATE.md` to
  `docs/adrs/LABS-NNNN-kebab-case-title.md` and add a row to `docs/adrs/README.md`.
- **Attribution is point-in-time.** `Author(s)` is frozen to who made the *original* decision; if you
  join by amending, add yourself to `Amended-by` (with your `role`/`harness`/`model` and the date),
  never to `Author(s)`.

See `docs/adrs/LABS-0000-how-we-use-cross-project-adrs.md` for the full convention. It adopts
`recipe-lint`'s `docs/adrs/0000-how-we-use-adrs.md` by reference and records only the cross-repo
deltas.

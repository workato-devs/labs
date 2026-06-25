# Cross-Project Architecture Decision Records

Decisions that govern a **relationship between two or more Workato Labs repos** — a contract, a
dependency, or a shared capability one repo provides and another consumes. Decisions internal to a
single repo live in *that repo's* `docs/adrs/`, not here.

These are **living records**: many are written as a hypothesis ahead of (or alongside)
implementation and carry dated amendments showing how the decision evolved. Verify an ADR's claims
against the current code in the affected repos before relying on them, and amend the ADR when a
change contradicts it. The conventions — including the cross-repo amend rule — are in
[LABS-0000 — How We Use Cross-Project ADRs](LABS-0000-how-we-use-cross-project-adrs.md). They adopt
`recipe-lint`'s `0000-how-we-use-adrs.md` by reference and record only the cross-repo deltas.

These records are repo-only Markdown and are **excluded from the published labs site**.

Start a new one by copying [`TEMPLATE.md`](TEMPLATE.md) to `LABS-NNNN-kebab-case-title.md` (next
number). IDs carry the `LABS-` prefix so a reference from another repo is never confused with that
repo's own ADR numbers.

| ADR | Title | Status | Affected repos | Summary |
|-----|-------|--------|----------------|---------|
| [LABS-0000](LABS-0000-how-we-use-cross-project-adrs.md) | How We Use Cross-Project ADRs | Accepted | `labs` + all | Conventions for hub-hosted cross-repo ADRs: scope, `LABS-` IDs, self-contained dependency map, heterogeneous per-repo back-references, and the cross-repo amend rule. Inherits `recipe-lint` 0000. |
| [LABS-0001](LABS-0001-skills-lint-rules-as-data.md) | Recipe Skills Declare Lint Rules as Data | Accepted | `recipe-skills` (producer), `recipe-lint` (consumer) | Each skill ships a `lint-rules.json` connector catalog as data; the linter consumes it via `--skills-path` for `ACTION_NAME_VALID`, `CONFIG_PROVIDER_MATCH`, `EIS_NO_CONNECTOR_INTERNAL`. The realized consumer of `recipe-lint` ADR-0004 — honest about where the data bar is and isn't met. |

**Status vocabulary:** `Proposed` (drafted) · `Accepted` (agreed, in effect) · `Superseded`
(replaced by a later ADR). Implementation is tracked separately via an optional `Implemented:` date
in each ADR's header.

# How We Use Cross-Project ADRs

**Author(s):** Zayne Turner, Claude [role: assistant; harness: Claude Code; model: Opus 4.8]
**Status:** Accepted
**Date:** June 17, 2026

**Affected repos:**
- `labs` (this hub) — home of cross-project ADRs.
- Any Workato Labs repo whose architecture is constrained by, or constrains, a decision recorded here (currently `recipe-lint`, `recipe-skills`).

---

## New to ADRs?

An **Architecture Decision Record** is a short, dated note capturing one significant decision — what
we chose, and *why* — at the moment we made it. If you've never used one, start with the linter's
[`docs/adrs/0000-how-we-use-adrs.md`](https://github.com/workato-devs/recipe-lint) in the
`recipe-lint` repo: it is the parent convention, and this document **adopts it by reference** rather
than restating it. What follows records only the things that are *different* because the decision
spans more than one repository.

## Context

Most architectural decisions belong to a single repo and live in that repo's `docs/adrs/`. But some
decisions govern a **relationship between repos** — a contract, a dependency, or a shared capability
one repo provides and another consumes. Those have no good home in either repo alone: put it in the
provider and the consumer can't see it; put it in the consumer and it's invisible to the next
consumer. Recorded in neither, the relationship is reconstructed from code each time someone touches
it — exactly the failure the linter's ADR-0004 documents.

So **cross-project decisions live here, in the labs hub**, and are referenced from the repos they
affect. The labs hub is already the place that describes the toolkit as a whole; the decisions that
bind its pieces together belong alongside it.

This document defines how we keep that record trustworthy across repo boundaries. The first such
decision is [LABS-0001](LABS-0001-skills-lint-rules-as-data.md).

## Decision

### 1. Scope — what belongs here vs. in a repo

Record an ADR here when the decision **governs a relationship across two or more labs repos**: a
data/API contract between them, a capability one provides and another depends on, or a shared
constraint that no single repo's ADRs can own. A decision internal to one repo stays in *that repo's*
`docs/adrs/`. When in doubt, ask "if I only read one repo, would I miss why this is the way it is?" —
if yes, it's cross-project.

### 2. Inherit the linter's `0000` by reference; the deltas are below

The parent conventions from `recipe-lint`'s `0000-how-we-use-adrs.md` apply **unchanged** here:
ADRs are living hypotheses (verify against code before relying on a claim); corrections are
**in-place, dated amendments**, never history rewrites; the status vocabulary is
`Proposed` / `Accepted` / `Superseded`, with implementation tracked separately via an optional
`Implemented:` line; and the **authorship & attribution schema** (`Author(s)` frozen to original
deciders, `Amended-by` for later contributors, a named human always accountable, agent provenance as
bracketed `role` / `harness` / `model` keys) is identical. The deltas — §3–§6 — exist only because
the record now crosses repo boundaries.

### 3. IDs and filenames carry a `LABS-` prefix

Cross-project ADRs are numbered `LABS-NNNN` (zero-padded, incremental; this meta-ADR is `LABS-0000`)
and filed as `LABS-NNNN-kebab-case-title.md`. The prefix is **load-bearing**: a reference from
another repo says "see `LABS-0001`," which can never be confused with that repo's own `0001`. Repo-
local ADRs keep their bare numbers; only cross-project ADRs are prefixed.

### 4. The ADR owns its own dependency map

Every cross-project ADR carries an **`Affected repos`** block in its header listing each repo it
governs and that repo's role (provider / consumer / constrained-by). This is the single authoritative
statement of the relationship. **We do not rely on every affected repo maintaining a back-reference**
— see §5 — so the map must be complete and current *here*. When the set of affected repos changes,
that's an amendment.

### 5. Back-references are per-repo and heterogeneous — never a forced convention

Labs repos are not uniform, and we do **not** impose ADR machinery on a repo that doesn't have it.
How an affected repo points back to a cross-project ADR is decided **per repo, in whatever form is
already natural there**:

- A repo **with** ADR infrastructure (e.g. `recipe-lint`) references the `LABS-NNNN` ADR in its own
  style — a `References:` line, an index row, or a note on the related local ADR.
- A repo **without** ADR infrastructure (e.g. `recipe-skills`) is **not** required to adopt ADRs to
  participate. At most it gets a lightweight link wherever documentation already lives. The decision
  is still fully recorded — here — because §4 makes this ADR self-contained.

Designing identical child-repo updates "for consistency" is a non-goal; the consistency we want is in
*the hub's* record, not in every repo's layout.

### 6. The cross-repo amend rule (replaces "same PR")

The linter's hard rule — *code that contradicts an ADR amends it in the same PR* — assumes the code
and the ADR live in one repo. Across repos they don't, so the rule becomes:

> **A change in any affected repo that contradicts a cross-project ADR must update that ADR (here),
> and cross-link the two changes.** The amendment lands as its own labs-repo change; reference it
> from the originating PR, and reference the originating PR from the amendment.

The labs ADR is the **source of truth**. If a repo's code and the ADR disagree, reality wins and the
ADR is amended per the inherited in-place-amendment rule (§2). Schema or contract fields that one repo
*reads* from another are the most common trigger: changing them is a cross-repo breaking change and
always warrants an amendment.

### 7. Repo-only, not published

These ADRs are governance docs, not site content. They live as Markdown under `docs/adrs/` and are
**excluded from the Jekyll build** (`_config.yml`). Keeping them out of the published site also avoids
entangling them with the hub site's licensing; they are decision records for contributors, not pages.

### 8. Template and index

- **Template:** copy [`TEMPLATE.md`](TEMPLATE.md) to `LABS-NNNN-kebab-case-title.md`.
- **Index:** [`README.md`](README.md) lists every cross-project ADR with status, affected repos, and a
  one-line summary; update it when adding an ADR.

## Alternatives considered

- **Keep the decision in the provider repo's ADRs** (e.g. record the skills relationship inside
  `recipe-lint`). Rejected: invisible to other consumers and to the skills repo itself; the provider
  becomes a dumping ground for relationships it doesn't own.
- **Duplicate the ADR into each affected repo.** Rejected: guarantees drift, and violates the single-
  source-of-truth principle the linter's `0000` is built on.
- **A standalone `decisions/` repo.** Rejected as premature: the hub already exists and already
  describes the toolkit as a whole. Revisit if the volume of cross-project ADRs outgrows the hub.

## Consequences

- Cross-cutting decisions have one trustworthy home, and an agent or contributor in any affected repo
  can find *why* a relationship is the way it is without reverse-engineering two codebases.
- The dependency map (§4) makes each ADR self-contained, which is what lets us honor heterogeneous
  repos (§5) instead of forcing uniform structure on them.
- The cost is a small cross-repo dance on amendments (§6) — two linked changes instead of one PR. The
  convention is kept deliberately light (a prefix, a header block, an index, one amend rule) so it's
  followed rather than routed around. This mirrors the linter's stance: lightweight on purpose.
- Supports the labs core principles — **Open** (a portable record, not buried in one tool), **Agent-
  First** (the dependency is legible to agents working in any affected repo), and **Contribute** (a
  contributor to one repo can see, and argue with, the decisions that constrain it).

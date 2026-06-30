<!--
Cross-project ADR template. Copy to docs/adrs/LABS-NNNN-kebab-case-title.md (next number,
zero-padded) and fill in. See LABS-0000-how-we-use-cross-project-adrs.md for the conventions —
in particular, this record is for decisions that span TWO OR MORE labs repos. A decision internal
to one repo belongs in that repo's own docs/adrs/. Delete these comments when done.
-->

# Title

**Author(s):** Name
<!-- Amended-by: only when someone joins via a later amendment. Original authors stay frozen above.
     Authorship schema is inherited from recipe-lint's 0000 §7 — see LABS-0000 §2.
**Amended-by:** Name [role: …; harness: …; model: …], dir. Name — Month YYYY
-->

**Status:** Proposed
**Date:** Month D, YYYY
<!-- Optional fields — keep only those that apply:
**Implemented:** Month D, YYYY      (when the decision was actually built)
**Superseded-by:** LABS-NNNN        (only if Status: Superseded)
**References:** ADR NNNN (Title) in <repo>, LABS-NNNN (Title)
-->

**Affected repos:**
<!-- REQUIRED. One line per repo this decision governs, with its role. This is the authoritative
     dependency map (LABS-0000 §4) — keep it complete; do not rely on the repos to back-reference. -->
- `repo-a` (provider) — what it provides.
- `repo-b` (consumer) — what it depends on.

<!-- Optional; add as the decision evolves (one line per dated amendment, newest first):
**Amendments:**
- Month YYYY — summary of what changed
-->

---

## Context

What cross-repo problem prompted this? Why does it belong in the hub rather than in one repo's ADRs
(LABS-0000 §1)? Capture the honest state of knowledge now — it may be a hypothesis later amendments
refine.

## Decision

What we decided, and why this option over the alternatives. Name the contract/dependency precisely
(schemas, flags, fields one repo reads from another) so a contributor or agent can act on it and
verify it against the code in each affected repo.

## Alternatives considered

What else was on the table, and why it lost. Keep *architectural* rejections inline so they aren't
re-litigated; a *scope* deferral belongs in a roadmap, not here.

## Consequences

What this makes easier, harder, or riskier across the affected repos. Call out the schema/contract
surface whose change would be a cross-repo breaking change (and thus trigger an amendment per
LABS-0000 §6). Note follow-on work and anything deliberately deferred.

<!--
When this evolves, do NOT rewrite the above. Add a dated amendment in place, add a matching line to
the Amendments header field, and (if a repo's code change drove it) cross-link the originating PR per
LABS-0000 §6. A later contributor goes on Amended-by, never on Author(s).
-->

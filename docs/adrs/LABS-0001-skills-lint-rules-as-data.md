# Recipe Skills Declare Lint Rules as Data

**Author(s):** Zayne Turner, Claude [role: assistant; harness: Claude Code; model: Opus 4.8]
**Status:** Accepted
**Date:** June 17, 2026
**Implemented:** June 17, 2026 — retroactive record of behavior already shipping (see Consequences)
**References:** ADR 0004 (Rules Are Data, Not Code) in `recipe-lint`; ADR 0001 (Tiered Validation) in `recipe-lint`

**Affected repos:**
- `recipe-skills` (producer) — authors a `lint-rules.json` per skill: the connector's valid action/trigger catalog, shipped as data.
- `recipe-lint` (provider/consumer) — exposes `--skills-path`, discovers those files, and feeds their data into three rules.

---

## Context

`recipe-lint`'s [ADR 0004 — *Rules Are Data, Not Code*] set a load-bearing bar inherited from the
linter's original PRD:

> **Changing the rules a customer runs must not require cutting a new Go binary.**

ADR-0004 names three load paths for rule data: embedded `builtin_rules.json`, project
`.wklint/rules/*.json`, and **skills `lint-rules.json` via `--skills-path`**. This ADR records the
third one — and the repo on the other side of it.

`recipe-skills` packages connector knowledge for AI coding agents. A skill teaches an agent *how* and
*when* to use a connector's actions; but an agent left to its own devices will **hallucinate action
and trigger names**. The skills repo's answer is a single source of truth per connector: a list of
the names that actually exist, audited against the live Workato connector. That list is exactly the
kind of artifact ADR-0004 says should be *data* — owned and shipped by the people closest to the
connector, on their own cadence, not bottlenecked behind a linter release.

Because this relationship lives in the seam between two repos, it had no decision record. A reader of
`recipe-lint` sees a `--skills-path` flag; a reader of `recipe-skills` sees `lint-rules.json` files;
neither repo alone explains that they are two ends of ADR-0004's "rules are data" contract. This ADR
makes that dependency explicit.

## Decision

**Each skill ships a `lint-rules.json` that declares its connector's lint rule data, and the linter
consumes it as data via `--skills-path`.** The governing principle, stated in the skills repo, is
*"lint rules own the **what**, instructions own the **how**."*

**What the skills repo produces.** Every skill directory contains a `lint-rules.json`. Its schema:

```json
{
  "version": "0.1.0",
  "connector": "gmail",
  "connector_internals": [],
  "valid_action_names": ["__adhoc_http_action", "download_attachment", "send_mail", "..."],
  "valid_trigger_names": ["new_email"],
  "action_rules": []
}
```

- `connector` — the provider this file describes.
- `valid_action_names` / `valid_trigger_names` — the audited, authoritative catalog. `SKILL.md`,
  `validation-checklist.md`, and templates **defer** to this file and never re-enumerate names.
- `connector_internals` — provider/field names that are connector-internal (skipped or flagged).
- `action_rules` — reserved slot for per-connector **declarative** assertions (ADR-0004's assertion
  vocabulary); currently empty in practice. This is the forward path, not yet exercised.

Files may be nested (e.g. `slack-recipes/slack_bot/lint-rules.json`,
`workato-recipes/variable/lint-rules.json`); each contributes its own catalog.

**What the linter does with it.** When invoked with `--skills-path <dir>`, the linter walks the tree
for `lint-rules.json` files and uses their data — and *only* that data — for three rules:

- `ACTION_NAME_VALID` — rejects action names not in `valid_action_names` for that provider.
- `CONFIG_PROVIDER_MATCH` — skips providers listed in `connector_internals`.
- `EIS_NO_CONNECTOR_INTERNAL` — flags connector-internal fields that appear in `extended_input_schema`.

The linter deliberately **does not** read `SKILL.md`, `validation-checklist.md`, templates, or
patterns. Those are for agents and humans; the data contract is `lint-rules.json` alone.

## Alternatives considered

- **Hardcode each connector's action/trigger lists in the linter binary.** Rejected — this directly
  violates ADR-0004's bar: every connector update would force a `recipe-lint` release, and connector
  teams would be blocked on the linter's cadence rather than shipping their own data. This is an
  *architectural* no.
- **Enumerate valid names in `SKILL.md` or other prose docs.** Rejected: duplication that drifts,
  isn't machine-checkable, and gives agents two sources to disagree on. `lint-rules.json` is the
  single source of truth precisely so the prose can defer to it.
- **A bespoke per-connector rule plugin/binary.** Rejected: reintroduces the binary-release coupling
  ADR-0004 exists to remove, for data that is fundamentally a list.

## Consequences

- **A connector team adds, renames, or audits an action by editing data** — `lint-rules.json` — with
  no `recipe-lint` release. For the action/trigger-catalog class of rules, the ADR-0004 bar is met,
  and `recipe-skills` is its primary realized consumer. This serves the labs **Contribute** principle
  (contribute connector coverage as data, not Go) and **Agent-First** (the catalog is what stops
  agents hallucinating names).

- **Honest scope of "rules are data" here.** The *data* (the catalogs) is shipped by skills; the
  *rule logic* — the three checks above — lives in the linter binary. So adding a new connector
  **name** is pure data, but a genuinely new *kind* of check is not: it needs either a Go change in
  the linter or population of the declarative `action_rules` slot, which today is empty. In ADR-0004's
  terms, skills exercise the declarative/data path for catalogs and parameters, not yet for novel rule
  logic. Stated plainly so the gap isn't mistaken for full coverage.

- **The `lint-rules.json` schema is a cross-repo contract.** The fields the linter reads —
  `connector`, `valid_action_names`, `valid_trigger_names`, `connector_internals`, and (when used)
  `action_rules` — are an interface between the two repos. Changing or removing any of them is a
  cross-repo breaking change and **must amend this ADR** per [LABS-0000](LABS-0000-how-we-use-cross-project-adrs.md) §6,
  cross-linking the change in whichever repo drove it.

- **Discovery is directory-walk, not a manifest.** The linter finds catalogs by walking
  `--skills-path` for `lint-rules.json`, so new and nested skills are picked up automatically — but a
  skill with no `lint-rules.json`, or one the path doesn't cover, is silently un-linted for names. The
  skills repo's `CONTRIBUTING.md` treats the file as REQUIRED for this reason.

- **Two surfaces to keep aligned.** This relationship is documented operationally in
  `recipe-skills/docs/cli-guidance.md` and `recipe-skills/README.md`; this ADR is the *decision*
  behind that documentation. If they diverge, this ADR and the linter's behavior are the sources of
  truth.

**Follow-on work (surfaced, not decided here):**

- Whether to populate `action_rules` with declarative assertions so skills can ship genuinely new
  *checks* as data — closing the gap above — or to keep skills limited to name catalogs. Either is a
  real decision and should get its own ADR or an amendment, not a silent default.

<!-- When this evolves, add a dated amendment in place; do not rewrite the above. -->

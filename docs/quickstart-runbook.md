# Quickstart Runbook — test script for `quickstart.html`

> **Status:** Fresh-machine test companion to `quickstart.html`. This is **not** site content
> (`docs/` is excluded from the Jekyll build). The page is the source of truth for copy; this
> runbook mirrors its arc for running the flow end-to-end and capturing real values.
>
> **Acceptance test (Principle 3):** run this start-to-finish on a clean laptop, ideally with a
> stranger. If the first five minutes feel rough, that's a bug — fix the page/runbook, not the tester.

---

## The story we're telling

**One sentence of intent → a deployed, proven `onboard_new_hire` API endpoint.**

The Workato difference we land at least once: anyone can wrap *one* API as a tool. Composing a
**governed, multi-system** orchestration — Salesforce + Slack — into a single agent-callable tool
is what only Workato's platform underneath makes possible.

**Portability framing (Principle 2 / 1.8):** every command can be shown in one agent (e.g. Claude
Code), but the copy says **"your agent"** — Claude, Cursor, Windsurf, Codex, or any agent. One named
tool among many, never the assumed entrypoint.

**Where the loop runs:** the build-and-test loop runs entirely in the terminal — the dev authorizes
the created connections once in the Workato UI. The self-test is a real HTTP call against the live,
governed endpoint (no mockups), made by the agent.

### The scenario (decisions locked)
Given a new hire's email, the automation:
1. Looks up their **Salesforce `User` record** (named persona end-to-end, e.g. *Avery Chen*).
2. Adds them to the **`#team-welcome`** Slack channel.
3. Posts a **welcome message** introducing them to the team.

Exposed through an **API-endpoint trigger** named **`onboard_new_hire`**, which **your agent calls
over HTTP and verifies** — the moment nobody else can show.

---

## The arc (9 beats — matches the page order)

| # | Beat | Surface | Who acts |
|---|------|---------|----------|
| 0 | Install + verify (recap → homepage) | brew / scoop | dev |
| 1 | Authenticate | `wk auth login --region trial` | dev |
| 2 | Load the recipe skills | recipe-skills (clone + load) | dev |
| 3 | Create your project | `wk init` | dev |
| 4 | Prompt the agent — one sentence | your agent | dev → agent |
| 5 | Compose the recipe | recipe-skills | agent |
| 6 | Lint-fix loop | `wk lint` | agent |
| 7 | Inspect the graph (optional) | visualizer | dev |
| 8 | Push / deploy | `wk push` | agent/dev |
| 9 | Expose as an API endpoint + agent self-test | `wk api …` + `curl` | agent |
| F | Footers — "if your agent gets stuck" + "what to build next" | — | — |

---

## Beat 0 — Install + verify (recap)

Don't re-teach install; link to the homepage `#install`. The page shows the macOS commands inline
plus a verify step. The two "things you might see" notes that strand newcomers:

- **Homebrew tap trust** — first `brew install workato-devs/tap/...` may prompt to tap/trust the
  third-party tap. Expected.
- **macOS publisher / quarantine** — manual binaries trip Gatekeeper ("cannot verify the developer").
  Fix: `xattr -d com.apple.quarantine /path/to/wk`. (Already in homepage manual tab.)

```bash
brew install workato-devs/tap/wk
brew install workato-devs/tap/recipe-lint
wk plugins install recipe-lint
# Recipe Visualizer — download the latest .vsix from the repo's Releases, then:
code --install-extension ./recipe-visualizer-1.0.0.vsix   # or cursor / windsurf
wk version
wk plugins list      # should list recipe-lint
```

> Recipe Visualizer is installed from a `.vsix` (not the marketplace yet) — grab the latest from
> [recipe-visualizer → Releases](https://github.com/workato-devs/recipe-visualizer/releases).

**No Workato account?** Sign up free: `https://app.trial.workato.com/users/sign_up_trial?utm_term=sign_in`
— a Developer Sandbox workspace. Trial users authenticate with `--region trial` in Beat 1.

---

## Beat 1 — Authenticate (`wk auth login`)

```bash
# --region MUST be a flag — running `wk auth login` bare fails (401s) before the prompts.
# Use trial for the free Developer Sandbox (paid: us|eu|jp|au|sg|il|cn).
wk auth login --region trial
# Interactive prompts, in order:
#   API token:        (paste your wrk_… token)
#   Environment:      name it yourself (e.g. dev)
#   Profile name:     [<region>-<workspace>-<env>]  ← enter to accept, or type your own
#                     (this is the profile alias stored in your keychain)

wk auth status     # verify connectivity (calls GET /users/me under the hood)
```

Token comes from the Workato UI: **Workspace admin → API clients** → create a Client Role (recipe /
connection / lifecycle perms) → create an API Client → copy the token (starts with `wrk`, shown once).
Walkthrough: `https://docs.workato.com/en/platform-cli.html#authentication`. CLI auth doc:
`wk` README → "Authenticate"; CI flags in `wk` `docs/ci-setup.md`.

---

## Beat 2 — Load the recipe skills

`git clone` recipe-skills, then load per the **skills repo's own instructions** (the canonical,
maintained source). The Labs quickstart links out rather than duplicating per-agent mechanics, so it
never drifts: `https://github.com/workato-devs/recipe-skills#quick-start`. For this build, load the
base skill plus **Salesforce** and **Slack**.

> Multi-agent loading (Claude Code / Codex / Cursor / Windsurf) and a root `AGENTS.md` for
> standard-aware auto-discovery are owned by recipe-skills —
> [recipe-skills#5](https://github.com/workato-devs/recipe-skills/issues/5). Labs quickstart has
> **no dependency** on it; it links to whatever the skills repo documents.

---

## Beat 3 — Create your project (`wk init`)

Scaffold the project **before** prompting, so the agent writes the recipe straight into a folder
already mapped to the workspace — no moving files mid-build. Run `wk init` from wherever you keep
working projects (e.g. `~/github`); it creates a project directory there with a `wk.toml`.

```bash
cd ~/github      # or wherever you keep working projects
wk init
# prompts (both are free-text you type — no picker):
#   Project name:   type a name (becomes the new directory)
#   Auth profile:   type the profile name from Beat 1 (run `wk auth list` if unsure)
cd <project-name>  # work from inside the new project
```

`wk.toml` maps local `*.recipe.json` files to a workspace folder. Everything the agent builds from
here lands in the project, so Beat 8 is just `wk push`.

---

## Beat 4 — Prompt the agent (one sentence)

With skills loaded and a project in place, state intent. The verbatim prompt on the page:

> "Build a Workato automation that takes a new hire's email, looks up their **User** record in
> **Salesforce**, adds them to the **#team-welcome** Slack channel, and posts a welcome message
> introducing them to the team. Expose it through an **API-endpoint trigger named `onboard_new_hire`**,
> then deploy it and call the live endpoint to confirm it works end to end."

**The agent grounds before it writes** (read-local + ask-you — there is *no* live workspace
introspection): it confirms exact **connection names**, verifies the `User` object/fields exist, and
reads 1–2 existing `*.recipe.json` for conventions. Answer with your real connection names.

---

## Beat 5 — Compose (`onboard_new_hire.recipe.json`)

> **Name the file `onboard_new_hire.recipe.json`** — the visualizer only opens on `*.recipe.json`
> (Beat 7 silently breaks otherwise).

Actions (all in the skills' `lint-rules.json` = source of truth):

- **Salesforce — find the user:** `search_sobjects` on `User`, filtered by email (no get-by-id action).
- **Slack (native, `provider: "slack"`):** `get_user_by_email` → `invite_user_to_conversation` →
  `post_message_to_channel`. Use `invite_user_to_conversation`, **not** `invite_user_to_channel`
  (linter rejects the latter).
- **Callable trigger:** an **API-endpoint trigger** so the recipe can be wrapped in an API collection
  (Beat 9). See `skills/workato-recipes/triggers/callable-recipe.md`.

---

## Beat 6 — Lint-fix loop (`wk lint`)

```bash
wk lint onboard_new_hire.recipe.json --skills-path ./recipe-skills/skills
# Exit codes:  0 = pass (warnings OK)   1 = lint error(s)   2 = invalid input
```

Agent reads structured diagnostics (`rule_id`, `level`, `message`, source pointer), fixes, re-lints
until exit `0`. Also runs automatically as a **`wk push` pre-push gate** (errors block). Fix-loop
*pattern* is in **recipe-skills `docs/cli-guidance.md`**, not the linter README.

---

## Beat 7 — Inspect the graph (optional human checkpoint)

Open `onboard_new_hire.recipe.json`, then any of: click the **preview icon** in the top-right of the
editor (same spot as Markdown preview; Cursor / Windsurf have the equivalent), **`Cmd+Shift+V`** /
**`Ctrl+Shift+V`**, or right-click → **"Visualize Recipe."** Click a node → details → **"Go to Code."**
Visualizer v1.0.0, purely human — no agent hook. An agent-confident dev can skip straight to deploy.

---

## Beat 8 — Push / deploy (`wk push`)

The project from Beat 3 already maps the recipe to the workspace, so deploy is one command. `wk push`
lints, then imports the recipe through Workato's **packaging APIs**.

```bash
wk push --dry-run       # preview what will be created
wk push                 # lints, then deploys
```

**Connections are created by the platform, not pre-built by the dev.** Because push imports through
the packaging APIs, the platform creates any missing Salesforce/Slack connections (and folders) as
part of the import. The dev's only hands-on bit is to authorize them *after* the push — open the recipe,
click the **Connections** tab, click each connection, and complete its sign-in in the right-hand panel.

Starting the recipe is the **agent's** job, not the dev's — it runs `wk recipes start` in Beat 9 once the
connections are live. (The recipe is API-endpoint-triggered, so there's no run/job to inspect until the
self-test actually calls the endpoint.)

---

## Beat 9 — Expose as an API endpoint + agent self-test

**The headline moment, terminal-only.** With connections authorized, the agent starts the recipe, wraps
it in an API collection + endpoint, mints a client key (printed once at creation — the agent captures it),
and calls the live endpoint.

```bash
wk recipes start <recipe-id>   # connections are live now → agent starts it (id from `wk status`)

wk api collections create --name "Onboarding API" --json     # prints the collection's base URL
wk api endpoints create endpoint.json --collection <id>      # bind POST /onboard → your recipe
wk api endpoints enable <endpoint-id>
wk api clients create --name "Onboarding" --collections <id>
wk api clients keys create <client-id> --name selftest --json  # ← auth token, shown once
```

`endpoint.json` = four fields the agent writes: `name`, `method` (`POST`), `path` (e.g. `onboard`),
`flow_id` (the recipe's ID).

The self-test — agent calls the live, governed endpoint and reads the result:

```bash
curl -X POST <collection-base-url>/onboard \
  -H "API-TOKEN: <key>" -H "Content-Type: application/json" \
  -d '{"email":"newhire@example.com"}'
# → {"success":true,"slack_user_id":"U…","channel_id":"C…","message_ts":"…"}
```

That call produces the recipe's first job — now there's a run to inspect:
`wk recipes jobs get <recipe-id> <job-id> --json` (full step traces, invaluable when the response
wasn't `success:true`).

✅ **Success criterion for the whole runbook:** `success:true` back over HTTP, new hire in
`#team-welcome` with a welcome message — no human in the loop.

**Optional — also expose as an MCP tool** (for chat-based agent clients): the same collection can back
an MCP server.

```bash
wk mcp servers create --name "Onboarding" --folder-id <id> --collection <id>
# Reveal its token in the UI: AI Hub → MCP Servers → User access → Developer MCP Token
claude mcp add onboarding <mcp-url>      # or your editor's MCP config
```

The curl self-test already proved the tool works — MCP just makes it callable from chat clients.

---

## Footer A — If your agent gets stuck (tested redirects)

One line of redirection puts the agent back on track. Real snags from the test run:

1. **Built a "callable recipe" / "genie skill" instead of an endpoint** → *"Rebuild it with an
   API-endpoint trigger."* A recipe-function or genie skill can't be self-tested over plain HTTP, and
   genie skills force identity-based MCP auth the CLI can't hand a token for.
2. **Salesforce search fails `MALFORMED_QUERY` / "field not present"** → *"Keep `sobject_name`,
   `limit`, and `field_list` out of the action's `extended_input_schema` — list only the SObject's real
   fields."* Those are reserved config keys; in the schema they leak into the SOQL `WHERE` clause.
3. **Can't add the new hire to `#team-welcome`** → *"Use `invite_user_to_conversation` (not
   `…_to_channel`), and resolve the channel to an ID via `conversations.list` — there's no add-by-name."*
4. **A Slack step returns `nil` / "no method 'where' for nil"** → *"Native Slack adhoc output isn't
   body-wrapped — reference `["channels"]`, not `["body","channels"]`."* (Only Workbot `slack_bot` adhoc
   uses a `body` wrapper.)
5. **`wk auth login` 401s before you can paste the token** → *"Pass `--region` as a flag (e.g.
   `--region trial`)"* — it can't be answered at a prompt, and a token from one region 401s against another.

---

## Footer B — What to build next

- More connector skills (Asana, Gmail, Jira, Stripe, Datatable) — the next build is one connector away.
- Community examples gallery — eventual home (roadmap 5.7).
- Discussions link; principles page.

---

## Pre-test checklist (the stranger's fresh machine)

- [ ] Install + verify done: `wk version`, `wk plugins list` shows recipe-lint, `recipe-skills` cloned,
      visualizer installed.
- [ ] A Workato workspace (free trial at app.trial.workato.com if needed; auth with `--region trial`)
      + API token. **Connections do NOT need pre-building** — `wk push` creates them; you authorize after.
- [ ] A Salesforce `User` record for the persona (e.g. Avery Chen) exists.
- [ ] A `#team-welcome` Slack channel exists; the Slack connection can invite + post.
- [ ] Self-test reaches `success:true` over HTTP against the live endpoint.

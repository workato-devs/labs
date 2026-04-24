# Workato Labs

**Build recipes with AI agents that actually work.**

An open-source developer toolkit for building, validating, visualizing, and managing Workato recipes — designed for humans and coding agents alike.

> 🟢 **Beta — Design Partner Program**

---

## The workflow

Four tools that replace the loop of guess, push, break, repeat.

**Agent writes recipe** (recipe-skills) → **Developer inspects** (visualizer) → **Linter validates** (`wk lint`) → **CLI pushes** (`wk push`)

---

## Install the toolkit

Five steps, each using a command you already know. No install scripts, no ambient dependencies, no magic.

### 1. Install the CLI

The `wk` binary — workspace operations, recipe management, and plugin system. Single binary, no dependencies.

```bash
# Download the binary for your platform from Releases, then:
tar xzf wk_*.tar.gz
sudo mv wk /usr/local/bin/
wk --version
```

📦 [Download from Releases →](https://github.com/workato-devs/wk-cli-beta/releases)

### 2. Install the recipe linter

Deterministic validation — catches errors that agent self-validation misses. Single binary, no dependencies.

```bash
# Download the binary for your platform from Releases, then:
tar xzf wk-lint_*.tar.gz
sudo mv wk-lint /usr/local/bin/
```

📦 [Download from Releases →](https://github.com/workato-devs/wk-lint-beta/releases)

### 3. Connect the linter to the CLI

Registers the recipe linter as a `wk` plugin so you can run validation from the CLI.

```bash
wk install recipes-lint
```

### 4. Clone the recipe skills

Agent-consumable knowledge for recipe authoring — connector config, datapill syntax, control flow, schemas. Also where connector-specific lint rules live. Point your coding agent here.

```bash
git clone https://github.com/workato-devs/recipe-skills.git
```

### 5. Install the recipe visualizer

IDE extension that renders recipe JSON as interactive workflow graphs. Works in VS Code, Cursor, and Windsurf.

Download the `.vsix` from this repo's [`downloads/`](https://github.com/workato-devs/labs/tree/main/downloads) folder, then:

```bash
# VS Code
code --install-extension ./recipe-visualizer-0.5.2.vsix

# Cursor
cursor --install-extension ./recipe-visualizer-0.5.2.vsix
```

---

## What's in the box

Each tool covers a piece of the developer lifecycle. Together, they replace manual recipe wrangling with an agent-native development flow.

**wk CLI** · Go
Single binary, zero dependencies. `wk pull`, `wk push`, `wk diff`, and `wk status` across workspaces. Plugin system for extending with custom commands.

**Recipe Linter** · Go
Deterministic validation via `wk lint`. Catches datapill syntax errors, schema mismatches, and structural issues that agents can't self-validate.

**Recipe Skills** · 7 connectors
Agent-consumable knowledge for recipe authoring. Connector config, datapill syntax, control flow, error handling. Also home to connector-specific lint rules. Point your coding agent at the skills directory.

**Recipe Visualizer** · .vsix
IDE extension rendering recipe JSON as interactive workflow graphs. Click a node, navigate to the source. Export graphs as images. VS Code, Cursor, Windsurf.

---

## Repositories

| Repo | Description |
|------|-------------|
| [workato-labs](https://github.com/workato-devs/labs) | Hub — docs, downloads, .vsix extensions |
| [wk](https://github.com/workato-devs/wk-cli-beta) | CLI — Go binary, workspace ops |
| [recipe-skills](https://github.com/workato-devs/recipe-skills) | Agent knowledge — 7 connector skill sets |
| [wk-lint](https://github.com/workato-devs/wk-lint-beta) | Recipe linter — deterministic validation |

---

## Feedback

Workato Labs is an open-source initiative from Workato. Community feedback welcome, no SLA.

File issues here for general toolkit feedback, or on the individual repos for tool-specific bugs.

[Join the conversation →](https://github.com/workato-devs/labs/discussions)

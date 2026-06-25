# The Developer Loop diagram

The canonical diagram of Labs tools in action — supporting both a **human developer**
and an **agent developer** in one quality loop. Used across launch assets (exec decks,
site, social/OG, talks).

## Files

| File | Role |
|---|---|
| `developer-loop.svg` | **Source of truth.** Hand-crafted, design-iterated. This is the artifact the designer edits and what ships in assets. Figma-importable, git-diffable. |
| `developer-loop.mmd` | Text-native **fallback** (Mermaid). For Arc `diagram` blocks and anywhere a plain-text source is wanted. Keep in sync with the SVG when the structure changes. |

The SVG is the master because the diagram outgrew Mermaid's auto-layout — the dual
human/agent track can't be expressed cleanly in `flowchart` syntax. The Arc spec
sanctions exactly this: "where Mermaid's auto-layout is insufficient, use a hand-crafted
SVG." The `.mmd` exists so docs/decks still have an accurate semantic source.

## What it shows

Recipe Skills feeds two things — grounding **context** to the AI Agent and **lint rules**
to `wk lint`. The loop: AI Agent → Write Recipe → Developer (reviews in Recipe Visualizer)
→ `wk lint` → Pass? → **Yes** ships via `wk push`; **No** routes to Fix → re-lint. `wk push`
loops back to the agent (ship + learn). Both a person and an agent are first-class actors
in the same loop; the Labs tools are the shared rails.

## Brand

Colors map to the canonical Labs tokens (see `index.html` / `principles.html`):

- **Labs tools** (Recipe Skills, wk lint, wk push) — royal blue `#1D60CA` on `#D3EEFA`
- **Human developer** (Recipe Visualizer) — teal `#0b8378` on `#B3FEF7`
- **Agent developer** (AI Agent) — purple `#6932ff` on `#efeaff`
- **Fix path** — icecream `#FC9867`, text `#b3500f`
- **Neutral / shared steps** — ink `#083763` on white

If brand tokens change, update both files together (per the brand-tokens convention).

## Provenance

Brought in from `~/Github/arc` for launch iteration:
- SVG: `arc/workflow-diagram.svg` (also embedded in `labs-exec-deck.html`)
- Mermaid: reconciled from `arc/labs-exec-deck.arc.md` (the original `flowchart LR`
  lacked the human/agent split — this version adds it to match the SVG)

The arc copies are decks-in-flight; this folder is the working source for the launch.

## Open questions for design collaboration

- **Dual entry lanes?** Today the human and agent share one loop with the human as a
  mid-flow gate. For a launch asset claiming both are first-class, consider symmetric
  entry lanes so neither reads as subordinate.
- **Product framing.** Should the tool nodes be visually grouped/labeled as the Labs
  product surface (wk · linter · Recipe Skills · Recipe Visualizer) rather than implied?
- **Standalone vs. slide scale.** It needs to read both at slide size and as a standalone
  social/OG asset. May need a captioned, higher-contrast variant.

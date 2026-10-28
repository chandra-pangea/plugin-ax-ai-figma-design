# plugin-ax-ai-figma-design

Three portable Figma **design** skills for coding agents — pure how-to for building a design system and its
screens in Figma via the Figma MCP (`use_figma`, `figma-use`, `figma-generate-design`,
`figma-generate-library`). No process, template, spec, VD-contract, PRD or ticket machinery — just how to
design Figma **tokens, components, and screens**.

One top-level folder per CLI (`claude/`, `codex/`), each shipping the same three skills. This is the layout the
ax-ai-platform plugin fetcher expects (`paths` default: `claude/`, `codex/`).

## Skills

| Skill | Builds | Key outputs |
|---|---|---|
| **figma-design-tokens** | The token layers as Figma variables | `BrandPrimitives` + `App-Primitives` (raw), `Semantics` + `ComponentTokens` (aliases), `DS/*` text + effect styles |
| **figma-design-components** | The component library | Atoms (`DS_Core_Components`), patterns (`DS_Core_Patterns`), templates (`DS_Core_Templates`) — full variant matrices, all token-bound, 1:1 base-DS parity |
| **figma-design-screens** | Production screens | Full layouts + IA + real content, every experience state as its own frame, motion wired as a Figma prototype; can also establish a design direction when no system exists yet |

They compose bottom-up: **tokens → components → screens**. Each skill verifies its inputs exist in the Figma
file and routes to the earlier skill if not.

## Register in ax-ai-platform

Add to `aatmix/config/plugins.yaml` (and the committed `plugins.example.yaml` / `deploy/config/plugins.yaml`):

```yaml
  - id: figma-design
    url: https://github.com/chandra-pangea/plugin-ax-ai-figma-design
    ref: main
```

No `paths` needed — each CLI reads its own top-level folder.

## Conventions
- Every visual property is **bound to a variable** — zero hardcoded hex/px. Primitives hold raw values;
  everything above them is an alias.
- `use_figma` is the only write path; load the Figma skills first. Scripts are atomic — on error, fix and retry.
- Idempotent and additive: everything is tagged with shared plugin data, re-runs add only what's missing.
- No dark mode / extra themes unless asked.

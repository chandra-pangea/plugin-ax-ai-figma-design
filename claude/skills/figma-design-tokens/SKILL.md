---
name: figma-design-tokens
description: Build a design system's token layers directly as Figma variables via the Figma MCP — primitive tokens (brand colors + typography, and the app foundation: grays, semantic ramps, spacing, radius, border, elevation, opacity, motion, z-index, breakpoints), then a semantic alias layer, component tokens, and semantic text + effect styles. Use when the user asks to "create design tokens", "set up Figma variables", "build primitive / semantic / component tokens", "turn brand guidelines into Figma variables", or "set up the token foundation for a design system". Pure how-to: it creates and binds variables and styles in Figma; it writes no spec, contract, PRD, or ticket.
metadata:
  version: "1.0"
---

# Design Tokens → Figma Variables

Build a design system's **token layers** as Figma variables, bottom-up, so every later screen and
component binds to a token and never to a raw value. Three layers, built in order:

```
Primitives (raw values)  →  Semantics (aliases)  →  Component tokens (aliases) + text/effect styles
```

| Layer | Collection(s) | Holds |
|---|---|---|
| **Primitives** | `BrandPrimitives`, `App-Primitives` | Raw values: brand color ramp(s) + type from the brand; grays, semantic ramps, spacing, radius, border, elevation, opacity, motion, z-index, breakpoints from best-in-class systems. |
| **Semantics** | `Semantics` | Role aliases to primitives — `color/surface/page`, `color/text/primary`, `color/action/primary/bg`, `space/inset/md`, `radius/control`, typography roles, etc. One hop. |
| **Component tokens** | `ComponentTokens` | Aliases to semantics for the high-theming-surface components (button, input, card, nav, table, modal, badge, tabs). |
| **Styles** | — | Semantic **text styles** (`DS/H1`…`DS/Code`) and elevation **effect styles** (`DS/Elevation/*`), bound to the typography/elevation variables. |

Decide up front how far to go: brand/foundation only (primitives), or the full token layer (primitives →
semantics → component tokens → styles). Primitives can exist on their own; semantics and component tokens
require primitives to alias.

## Hard guardrails (read first)

1. **Primitives are raw values; everything above is an alias.** `Semantics` and `ComponentTokens` variables
   are **always** `VARIABLE_ALIAS` — zero raw values. A semantic aliases a primitive (one hop); a component
   token aliases a semantic (one hop). If a value you need has no primitive, **add the primitive first** —
   never alias to nothing and never write a raw value above the primitive layer.
2. **Only touch the token collections and their styles.** Create/modify only `BrandPrimitives`,
   `App-Primitives`, `Semantics`, `ComponentTokens`, and the `DS/*` text/effect styles. Never modify nodes,
   pages, components, or other collections.
3. **Scopes are always explicit** (never left at `ALL_SCOPES`). Primitives get *functional* scopes so they're
   bindable while you build; once a semantic layer exists, re-scope primitives to `[]` (gated — ask first) so
   designers bind only semantics. Reference-only groups (z-index, breakpoints, motion) get `[]`.
4. **Brand guidelines win.** Any value the brand specifies beats the reference-system default for that token.
5. **One mode.** Primitives use one mode `Value`; `Semantics`/`ComponentTokens` use one mode `Default`. No
   dark mode or extra themes unless the user explicitly asks.
6. **Idempotent, additive, skip-by-default.** Tag everything (`setSharedPluginData`). On a re-run, add only
   what's missing; skip + report anything present. Only overwrite when the user explicitly asks to update.
7. **`use_figma` is the only write path.** Load the `figma-use` skill before any `use_figma` call and include
   it in `skillNames`. Scripts are atomic — on any error nothing was written: read it, fix, retry. Build in
   chunks (≤ ~15 variables or one style group per script) and validate after each.
8. **Confirm before writing.** Show the resolved build plan (collections, groups, exact names + values +
   types + scopes) and get a go-ahead before the first write.

## Required inputs — ask if missing, stop until provided

1. **Figma file link** — identifies the target file (variables are file-scoped, not page-scoped).
2. **Brand guidelines** — for the primitive brand layer. Accept any form (Figma link, image, PDF, text); see
   `references/brand-ingest.md`.
3. **A chosen look / design direction** — *only if building semantics.* The semantic catalog marks some alias
   targets `DIRECTION` (which primitive plays "primary action background", which surface is the page, etc.).
   Resolve those from the chosen direction's screens if one exists in the file, else from the user's stated
   look, else the catalog fallback (say which you used). Primitives alone need no direction.

## Workflow

### Phase 0 — Target & scope
Confirm the file; state it back ("I'll write variables into file **X**"). Decide the scope: primitives only,
or the full token layer. Run the discovery script (`references/figma-scripts.md` §Discover) to see which
collections/variables already exist → build the missing-only delta.

### Phase 1 — Brand primitives
Follow `references/brand-ingest.md`: parse the guidelines into brand colors + typography, detect gaps
(Pantone-only, no neutral, no type scale, named-not-numeric weights), propose a specific value for each gap and
**confirm** before using it. Build `BrandPrimitives` (`references/figma-scripts.md` §BrandPrimitives).

### Phase 2 — App primitives
Build `App-Primitives` per `references/primitive-catalog.md`: grays + semantic ramps (WCAG-checked), spacing,
radius, border, decomposed elevation, opacity, motion, z-index, breakpoints. **Web-fetch the drift-prone
numeric sets** (Atlassian spacing/radius/border, Material 3 elevation + motion, Tailwind breakpoints) before
building; fall back to the catalog baselines only if a fetch fails, and say so. Build in chunks, validate each.

### Phase 3 — Semantics (if in scope)
Resolve every `DIRECTION`-marked target into a **Direction Resolution Table** (role → primitive → source).
Build `Semantics` as aliases per `references/semantic-catalog.md`; chunk and validate.

### Phase 4 — Component tokens + styles (if in scope)
Build `ComponentTokens` aliasing `Semantics`. Create the 12 `DS/*` text styles (bound to the typography
variables) and the 3 `DS/Elevation/*` effect styles (from the decomposed elevation semantics). See
`references/figma-scripts.md` §Styles.

### Phase 5 — Re-scope (gated) & report
With semantics live, propose re-scoping every primitive to `[]` so designers bind only semantics — execute
only on explicit confirmation (scopes only; never values/names). Run the validation script and report:
collections touched, created vs skipped counts, groups per collection, WCAG summary for grays + semantics,
brand gaps filled with confirmed values.

## Key technical rules (baked into the scripts)

- **Colors are `{r,g,b,a}` in 0–1** (not 0–255). Use the `hexToRgba` helper; alpha from `#RRGGBBAA`.
- **`/` in a name is the group** — `color/brand/primary` → group `color` › `brand`. Naming = grouping.
- **Types:** COLOR; FLOAT (spacing, radius, border, sizes, opacity, durations, z-index, breakpoints, shadow
  offsets/blur/spread, numeric weight); STRING (font family, easing curves).
- **Shadow is decomposed** into FLOAT primitives (`elevation/{level}/y|blur|spread`) + a COLOR
  (`elevation/{level}/color`) — Figma has no shadow variable type. Effect *styles* compose them later.
- **Always `return` created/mutated IDs** from each `use_figma` script, and tag each item for idempotency.
- Optional (offer, off by default): `setVariableCodeSyntax('WEB', 'var(--…)')` for code pipelines.

## Reference files
- `references/brand-ingest.md` — parse any brand-guideline format; gap detection; propose-then-confirm.
- `references/primitive-catalog.md` — primitive names/groups/types/scopes/baseline values + source URLs.
- `references/semantic-catalog.md` — the semantic + component-token alias catalog (names, targets, scopes).
- `references/figma-scripts.md` — copy-paste `use_figma` scripts: helpers, discover, build, styles, validate.

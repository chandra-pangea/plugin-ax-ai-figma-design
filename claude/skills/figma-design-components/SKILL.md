---
name: figma-design-components
description: Build a design system's component library in Figma via the Figma MCP — atoms, patterns, and layout templates as component sets with full variant matrices, every property bound to a semantic or component token, each mappable 1:1 to a named base design system (Material 3, shadcn, …) so code wrappers are buildable. Use when the user asks to "build the component library in Figma", "create components from the design system", "make Figma components with variants", "build atoms / patterns / templates", or "turn the design tokens into components". Requires the token layers to already exist as Figma variables (see figma-design-tokens). Pure how-to: it creates components in Figma; it writes no spec, contract, or ticket.
metadata:
  version: "1.0"
---

# Design System Components → Figma component library

Build the reusable building blocks that let any screen be assembled from instances and never hand-drawn.
Components live on three pages, built in order:

```
tokens (figma-design-tokens)  →  atoms (DS_Core_Components)  →  patterns (DS_Core_Patterns)  →  templates (DS_Core_Templates)
```

| Tier | Page | What |
|---|---|---|
| **Atoms** | `DS_Core_Components` | Single-purpose controls: Button, Input, Checkbox, Badge, Avatar, Spinner, … (~26 sets). |
| **Patterns** | `DS_Core_Patterns` | Compositions of atom **instances**: FormField, Menu, Card, Table, Tabs, Alert, Modal, SidebarNav, … (~23 sets). |
| **Templates** | `DS_Core_Templates` | Page layouts from pattern/atom instances with labelled slots: AppShell, Dashboard, ListPage, FormPage, … (9 frames). |

## Hard guardrails (read first)

1. **Zero hardcoded values.** Every fill, text color, stroke, spacing, padding, gap, radius, border width,
   shadow and typographic property is bound to a `Semantics`/`ComponentTokens` variable or a `DS/*` text/effect
   style. If a value has no token, **stop and add the token** (via `figma-design-tokens`) — never hardcode,
   never bind a raw primitive on a component.
2. **Reuse up the tiers.** A pattern composes **instances** of atoms (a FormField contains the real Input
   component) — never redraws an atom. A template composes instances of patterns/atoms. Never detach.
3. **Full variant matrices.** Each component is a **component set** (`combineAsVariants`) with the exact variant
   property names/values the catalog lists — not a "representative subset". Build one set per script; after each,
   screenshot and spot-check bindings.
4. **1:1 base-design-system parity.** Every component maps to a named component of a chosen base DS (Material 3
   / shadcn / Ant / Chakra …) so a thin code wrapper is buildable. If the look demands anatomy the base DS can't
   express, surface it and resolve with the user (composite wrapper / adjust / documented custom) — never
   silently improvise. See `references/base-ds-parity.md`.
5. **Native to the look.** Components must look indistinguishable beside the chosen design direction's screens —
   same radius personality, density, color roles, elevation, icon family.
6. **Auto-layout everywhere; real content.** Text uses `DS/*` styles; icons from one family at `size/icon/*`.
   No lorem, no "Label" — real labels so layout truth shows.
7. **No dark mode / extra modes** unless asked.
8. **Idempotent, additive.** Tag everything (`setSharedPluginData('ax-dsc', …)`). On a re-run add only what's
   missing; never overwrite without explicit instruction. This is how the library grows.
9. **`use_figma` is the only write path.** Load `figma-use` AND `figma-generate-library` before any `use_figma`
   call; include them in `skillNames`. Scripts are atomic — on error, read it, fix, retry. Chunk + screenshot.
10. **Touch only the `DS_Core_*` pages.** Never modify tokens' values, the design-direction pages, or screens.

## Required inputs — validate first, ask and stop if missing

1. **Figma file link** — the target file.
2. **Token layers present** — verify by inventorying variables: `Semantics` (+ `ComponentTokens`) and the
   `DS/*` styles must exist with usable coverage. Missing → stop, route to `figma-design-tokens`.
3. **A chosen look / design direction** — the visual personality the components must match (its Figma page if
   one exists, else the user's stated look). Screenshot it and absorb radius, density, color roles, elevation,
   icon family, nav model.
4. **Base design system** — a named system for wrapper parity (guardrail 4).

## Workflow

### Phase 0 — Inputs, discovery, absorb the look
Validate inputs. Inventory existing tokens, `DS_Core_*` pages, and any `ax-dsc`-tagged components (→ build only
the delta). Screenshot the look and extract its component-styling rules (radius personality, elevation strategy,
density, nav model, icon family) into a working note.

### Phase 1 — Base-DS review & parity table
Follow `references/base-ds-parity.md`: web-fetch the base DS docs; per catalog component extract anatomy,
variant/prop API, states, sizes; produce the **parity table** (Figma set ↔ base-DS component + prop mapping).
Flag any component the base DS lacks and resolve it per guardrail 4.

### Phase 2 — Expert decisions
Decide (like a senior product designer) what the look doesn't already settle: default density, control sizing
defaults, table ergonomics, form patterns (label position, error placement), empty/error philosophy, icon-only
vs labeled actions, destructive-action treatment. State decisions; don't re-ask routine ones.

### Phase 3 — Atoms (`DS_Core_Components`)
Build every Tier-1 set from `references/component-catalog.md` with its full variant matrix, auto-layout, all
properties token-bound, real labels. Each set in a documentation frame (name, base-DS mapping, usage note,
do/don't). One set per script; screenshot; bindings audited at phase end (`references/quality-checklist.md`).

### Phase 4 — Patterns (`DS_Core_Patterns`)
Build Tier-2 sets composing atom **instances**. Same documentation/chunking/audit. Add any look-specific
patterns the catalog's "direction delta" calls for (command palette, stepper, dropzone, calendar, …).

### Phase 5 — Templates (`DS_Core_Templates`)
Build Tier-3 templates from real pattern/atom instances at the look's platform size, with clearly labelled
slot placeholders (`surface/sunken` dashed regions) for screen designers.

### Phase 6 — Self-critique & conformance
Run `references/quality-checklist.md` in full: per-component (every variant screenshot-checked), WCAG AA on
every interactive state, the **zero-hardcoded-value audit** (scan all `DS_Core_*` nodes for unbound
paint/number properties), and the **conformance check** (sample components beside the look's screens — any seam
is a defect). Fix everything; report honestly: counts built vs skipped vs deferred, WCAG table, parity gaps
resolved.

## Reference files
- `references/component-catalog.md` — the component manifest: 3 tiers, exact variant matrices, bindings, base-DS mapping.
- `references/base-ds-parity.md` — reviewing the base DS and building the parity table.
- `references/figma-build.md` — `use_figma` patterns: component sets, variable binding, instances, styles, tagging, the audit script.
- `references/quality-checklist.md` — the Phase 6 checks.

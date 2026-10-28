# Base design system review & parity (Phase 1)

> Part of `figma-design-components`. Every Figma component is implemented in code as a **thin wrapper over the
> base design system** the user named. This review guarantees that mapping exists *before* anything is drawn.

## 1. Fetch the real docs — never work from memory

Web-fetch the component index and per-component pages for every Tier-1/Tier-2 catalog component:

| Base DS | Where |
|---|---|
| shadcn/ui | https://ui.shadcn.com/docs/components |
| Material 3 | https://m3.material.io/components (anatomy, states, measurements tabs) |
| Ant Design | https://ant.design/components/overview |
| Chakra UI | https://chakra-ui.com/docs/components |
| Other | ask the user for the docs root, then fetch |

If a fetch fails, say so and fall back to the catalog's M/S columns — flag reduced confidence in the table.

## 2. Extract, per component
- **Anatomy** — the parts the base DS defines (container, label, leading icon, supporting text…). Mirror those
  part names in the Figma layer structure so wrappers/Code Connect are trivial.
- **Variant & prop API** — the real prop names/values (`variant="outline"`, `size="sm"`). Figma variant
  properties keep the catalog's Title-case names; the table records the exact prop mapping.
- **States** — which states the base DS implements natively (hover, focus-visible, disabled, loading…). Don't
  design a state the base DS can't produce without noting the wrapper work.
- **Sizes & metrics** — control heights, paddings. Where the look's density differs, the token layer absorbs
  it (that's what wrappers override); note any metric the base DS hardcodes.

## 3. Parity table (one row per component)

| Figma set | Base-DS component | Prop mapping | Gaps / wrapper notes |
|---|---|---|---|
| `Button` | shadcn `button` | Tone→variant (Primary→default, Secondary→outline, Ghost→ghost, Danger→destructive); Size→size | Loading needs wrapper spinner |

## 4. Gap protocol

When a component has **no base-DS equivalent**, choose with the user, per component:
1. **Composite wrapper** — build from base-DS primitives (preferred; note the recipe).
2. **Adjust the design** — only if the look permits.
3. **Documented custom component** — last resort; record the build cost.

Never silently design something un-implementable, and never drop a component to avoid the question.

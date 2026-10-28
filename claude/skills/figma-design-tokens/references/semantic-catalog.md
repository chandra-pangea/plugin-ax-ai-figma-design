# Semantic + component-token catalog

> Part of `figma-design-tokens`, Phases 3–4. The target set of role tokens to create. Build every row (or
> record a per-item waiver with the user); a row already present is skipped + reported. Counts are a
> completeness guide, not a contract.

Conventions:
- `Semantics` and `ComponentTokens` have exactly **one mode `Default`**. Every variable is an **alias**
  (`setValueForMode` with `{type:'VARIABLE_ALIAS'}`) — never a raw value.
- **Alias target** column: a concrete primitive name = default target. **`DIRECTION`** = resolve from the
  chosen look/direction (its screens, or the user's stated look); the value in parentheses is the fallback if
  nothing decides it (say when you fall back). Ramp steps refer to the actual foundation ramps — verify names
  first and adapt step numbers to the ramp that exists.
- Scopes are set explicitly per the Scope line. ComponentTokens inherit the scope rules by type.
- Names use `/` grouping exactly as written.

Approximate totals: `Semantics` **~187**, `ComponentTokens` **~60**, text styles **12**, effect styles **3**.

## Collection `Semantics`

### color/surface — 7 · scope `["FRAME_FILL","SHAPE_FILL"]`
| Variable | Alias target |
|---|---|
| `color/surface/page` | DIRECTION (`color/gray/50` or white) |
| `color/surface/raised` | DIRECTION (white / `gray/0`) |
| `color/surface/sunken` | DIRECTION (`color/gray/100`) |
| `color/surface/overlay` | same as raised unless DIRECTION differs |
| `color/surface/inverse` | `color/gray/900` |
| `color/surface/brand-subtle` | DIRECTION (`color/brand/primary/50`) |
| `color/surface/disabled` | `color/gray/100` |

### color/text — 9 · scope `["TEXT_FILL"]`
`primary` → DIRECTION (`gray/900`) · `secondary` → `gray/700` · `tertiary` → `gray/500` · `disabled` →
`gray/400` · `placeholder` → `gray/400` · `inverse` → `gray/50` · `on-brand` → DIRECTION (white, AA on action
bg) · `link` → DIRECTION (`brand/primary/600`) · `link-hover` → one step darker than link.

### color/icon — 5 · scope `["SHAPE_FILL","TEXT_FILL"]`
`default` → `gray/700` · `subtle` → `gray/500` · `disabled` → `gray/400` · `inverse` → `gray/100` · `brand` →
DIRECTION.

### color/border — 6 · scope `["STROKE_COLOR"]`
`default` → `gray/300` · `subtle` → `gray/200` · `strong` → `gray/400` · `disabled` → `gray/200` · `focus` →
DIRECTION (brand, AA on page) · `inverse` → `gray/700`.

### color/action — 21 · scope `["FRAME_FILL","SHAPE_FILL","TEXT_FILL","STROKE_COLOR"]`
| Group | Variables | Targets |
|---|---|---|
| `primary/` | `bg, bg-hover, bg-pressed, bg-disabled, text, text-disabled` | DIRECTION brand step for `bg`; hover/pressed = ±1/±2 steps; disabled bg → `gray/200`; text → `text/on-brand`; text-disabled → `gray/400` |
| `secondary/` | `bg, bg-hover, bg-pressed, border, border-hover, text, text-disabled` | bg = surface/raised; hover/pressed = `gray/50`/`gray/100`; border = `gray/300`; text = `text/primary` |
| `ghost/` | `bg-hover, bg-pressed, text, text-disabled` | hover `gray/100`, pressed `gray/200`, text = DIRECTION |
| `danger/` | `bg, bg-hover, bg-pressed, text` | `red/600`, `red/700`, `red/800`, white |

### color/status — 24 · scope `["FRAME_FILL","SHAPE_FILL","TEXT_FILL","STROKE_COLOR"]`
Per tone `{success→green, warning→yellow, danger→red, info→blue}` create 6: `bg-subtle` (50/100) · `text`
(700+, AA on bg-subtle) · `border` (300/400) · `icon` (600) · `bg-bold` (600; warning may need 400/500 + dark
text) · `text-on-bold` (white, or `gray/900` on warning).

### color/selected — 4
`bg` → DIRECTION (`brand/primary/50`) · `bg-hover` (darker) · `text` (brand 700) · `border` (brand 600).

### color/focus + color/overlay — 2
`focus/ring` → same as `border/focus` (scope stroke ∪ effect-color) · `overlay/scrim` → DIRECTION (`gray/900`,
applied at `opacity/scrim`; scope frame-fill).

### typography — 51 · scopes by kind (family `["FONT_FAMILY"]`, size `["FONT_SIZE"]`, weight `["FONT_WEIGHT"]`, line-height `["LINE_HEIGHT"]`, letter-spacing `["LETTER_SPACING"]`)
12 roles: `display, h1, h2, h3, h4, body-lg, body, body-sm, label, caption, overline, code`. Each role gets 4
(`typography/<role>/family|size|weight|line-height`) aliasing `font/*` per the direction's pairing. Plus 3
letter-spacing vars (`display`, `overline`, `caption`). `code/family` needs a mono primitive (flag if absent).
12×4 + 3 = 51.

### space — 17 · scope `["GAP","WIDTH_HEIGHT"]`
`inset/{xs,sm,md,lg,xl}` → 4,8,12,16,24 · `stack/{xs,sm,md,lg,xl}` → 4,8,12,16,24 · `inline/{xs,sm,md,lg}` →
4,8,12,16 · `layout/{page-margin,gutter,section-gap}` → DIRECTION grid.

### radius — 5 · scope `["CORNER_RADIUS"]`
`control-sm, control, surface, overlay, pill` — DIRECTION radius personality (`pill` → the max primitive).

### border-width — 3 · scope `["STROKE_FLOAT"]`
`default` → 1 · `strong` → 2 · `focus` → 2.

### elevation — 12 · scopes y/blur/spread `["EFFECT_FLOAT"]`, color `["EFFECT_COLOR"]`
3 roles × 4: `raised/{y,blur,spread,color}`, `overlay/{…}`, `modal/{…}` aliasing the direction's chosen
primitive levels (fallback level-1/3/5).

### size — 6 · scope `["WIDTH_HEIGHT"]`
`control/{sm,md,lg}` → 32,40,48 · `icon/{sm,md,lg}` → 16,20,24.

### motion — 7 · scope `[]`
`duration/{fast,base,slow,exit}` + `easing/{standard,enter,exit}` aliasing `motion/*`.

### opacity — 2 · scope `["OPACITY"]`
`disabled` ≈0.4 · `scrim` ≈0.5.

### z — 6 · scope `[]`
`dropdown, sticky, overlay, modal, popover, toast` → matching `z-index/*`.

> Count: 7+9+5+6+21+24+4+2+51+17+5+3+12+6+7+2+6 = **187**. When validating, enumerate the rows — don't trust
> the arithmetic.

## Collection `ComponentTokens` — 60 · aliases into `Semantics` only

Component tokens exist only for the 8 high-theming-surface components; everything else binds semantics directly.

| Group | Variables |
|---|---|
| `button/` (8) | `height-sm`→`size/control/sm`, `height-md`, `height-lg`, `pad-x-sm`→`space/inset/sm`, `pad-x-md`, `pad-x-lg`, `gap`→`space/inline/sm`, `radius`→`radius/control` |
| `input/` (12) | `height`→`size/control/md`, `pad-x`→`space/inset/md`, `radius`→`radius/control`, `bg`→`surface/raised`, `border`→`border/default`, `border-hover`→`border/strong`, `border-focus`→`border/focus`, `border-error`→`status/danger/border`, `text`→`text/primary`, `placeholder`→`text/placeholder`, `gap-label`→`space/stack/xs`, `gap-help`→`space/stack/xs` |
| `card/` (5) | `bg`→`surface/raised`, `border`→`border/subtle`, `radius`→`radius/surface`, `inset`→`space/inset/lg`, `gap`→`space/stack/md` |
| `nav/` (11) | `width`→DIRECTION, `item-height`→`size/control/md`, `item-radius`→`radius/control`, `item-gap`→`space/stack/xs`, `bg`→DIRECTION, `item-text`→`text/secondary`, `item-text-active`→DIRECTION, `item-bg-hover`→`action/ghost/bg-hover`, `item-bg-selected`→`selected/bg`, `item-indicator`→`icon/brand`, `inset`→`space/inset/sm` |
| `table/` (8) | `header-bg`→`surface/sunken`, `header-text`→`text/secondary`, `row-height-compact`→`size/control/sm`, `row-height-default`→`size/control/md`, `row-border`→`border/subtle`, `row-bg-hover`→`action/ghost/bg-hover`, `row-bg-selected`→`selected/bg`, `cell-pad-x`→`space/inset/md` |
| `modal/` (7) | `bg`→`surface/overlay`, `radius`→`radius/overlay`, `inset`→`space/inset/xl`, `max-w-sm`, `max-w-md`, `max-w-lg` (alias a `breakpoint/*` primitive or flag), `scrim`→`color/overlay/scrim` |
| `badge/` (3) | `height`, `pad-x`→`space/inset/xs`, `radius`→DIRECTION (`radius/pill` or `control-sm`) |
| `tabs/` (6) | `height`→`size/control/md`, `gap`→`space/inline/lg`, `indicator-color`→`icon/brand`, `indicator-thickness`→`border-width/strong`, `text`→`text/secondary`, `text-active`→`text/primary` |

A component token (e.g. `modal/max-w-*`) must alias a primitive/semantic — never a hardcoded number. If no
primitive backs it, add the primitive first.

## Styles (created from semantics)
- **12 text styles** — `DS/<Role>` (`DS/Display`, `DS/H1`…`DS/Code`), each with family/size/weight/line-height
  (+ letter-spacing where defined) **bound to the `typography/<role>/*` variables**.
- **3 effect styles** — `DS/Elevation/Raised|Overlay|Modal`, drop shadows composed from the
  `elevation/<role>/*` variables (y/blur/spread/color bound).

## Direction Resolution Table (Phase 3)
Before building semantics, output one row per `DIRECTION`-marked target: variable → resolved primitive →
source (the direction's screen, the user's stated look, or "fallback"). Approve it, then build.

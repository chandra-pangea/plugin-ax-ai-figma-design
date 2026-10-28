# Brand guidelines — ingest, extract, fill gaps

> Part of `figma-design-tokens`, Phase 1. Turn whatever the user gives you into clean **brand color** +
> **typography** tokens for `BrandPrimitives`, flag anything critical that's missing, and never use an
> invented value without explicit confirmation.

## Accept any input format

| Input | How to read it |
|---|---|
| **Figma link** | Load `figma-use` and read the file: existing variables (`getLocalVariablesAsync`), paint/text styles, brand/logo/typography frames. Extract hex from fills and family/size/weight from text styles. |
| **Image** (brand sheet) | It's in context — read colors, hex, font names, and the type scale directly. |
| **PDF** | Extract text + rasterize pages to read swatches, hex, font specs. |
| **Plain text** | Parse for hex codes, color names, font families, weights, sizes. |

Normalize everything to a list of `{name, hex}` brand colors and a typography set (families, weights, sizes,
optional line-heights/letter-spacing). Map hex → `{r,g,b,a}` later via `hexToRgba`.

## Naming the brand tokens

- Flat brand colors → `color/brand/{role}` (`color/brand/primary`, `/secondary`, `/accent`).
- A brand ramp → `color/brand/{hue}/{step}` (`color/brand/primary/600`).
- Keep the guidelines' own names where sensible; otherwise use the roles above and show the mapping in the plan.

## Critical-gap checklist — detect, propose, CONFIRM

For each gap, present a **specific proposed value** and **wait for a yes** before using it:

1. **Colors not in sRGB hex** (Pantone/CMYK/RGB-255): Figma COLOR needs 0–1 sRGB. Propose the hex conversion
   and confirm (255 → ÷255; Pantone/CMYK → closest sRGB hex, flagged approximate).
2. **No neutral/gray**: gray lives in `App-Primitives`; ask whether the neutral is **pure** or **tinted**
   toward the brand hue (default pure). Confirm.
3. **No type scale**: propose a modular scale (12/14/16/18/20/24/30/36/48) and confirm.
4. **Weights named not numeric** ("Semibold"): propose Regular 400 / Medium 500 / Semibold 600 / Bold 700.
5. **Only one brand hue**: propose a 50–900 ramp around it, or keep a single `color/brand/primary`. Confirm.
6. **Font family not in Figma**: the STRING variable still stores the name — flag that text binding needs the
   font installed.
7. **Alpha / overlay brand colors**: capture alpha into the `a` channel (`hexToRgba` handles `#RRGGBBAA`).

## Precedence

Where the brand specifies a value that also appears in the reference systems, **the brand value wins**. Note
the override in the build plan.

## Output of this phase

A confirmed structure ready for the `BrandPrimitives` build script's `BRAND` object:
```
colors:        [{ name, hex[, alpha] }]
families:      [{ name: 'font/family/…', value }]
weights:       [{ name: 'font/weight/…', value }]
sizes:         [{ name: 'font/size/…',   value }]
lineHeights:   [{ name: 'font/line-height/…', value }]    // only if defined
letterSpacing: [{ name: 'font/letter-spacing/…', value }] // only if defined
```
Plus a short list of every gap filled and the confirmed value used.

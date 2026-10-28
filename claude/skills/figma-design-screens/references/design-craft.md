# Design craft — look & feel, mood → parameters, divergence

> Part of `figma-design-screens`. Used in Phase 3, mainly in **[Direction]** mode (establishing a look) but the
> craft rules apply to every screen. Turn vague inputs ("modern and clean") into concrete, buildable parameters.

## The design interview (ask the sharp ones only)

Skip anything already answered by the brand, requirements, or references. Offer 2–3 concrete options over open
questions — easier to react than invent.

1. **Audience & context** — who, how often, where? (Power users in a dense admin vs. an occasional mobile
   consumer changes density, target size, hierarchy.)
2. **Personality — 3 adjectives** — e.g. *calm, editorial, trustworthy* / *bold, energetic, modern* / *warm,
   friendly* / *precise, technical, dense*. These become the thesis.
3. **Emotional goal** — what should a user feel in 5 seconds (confident? impressed? at ease? in control?)?
4. **References liked & disliked** — and *why*; it reveals the real preference.
5. **Differentiation** — which competitors should this *not* look like?
6. **Density** — airy, balanced, or compact?
7. **Tone** — playful↔serious, expressive↔restrained, decorative↔minimal.
8. **Shape** — sharp/architectural, softly rounded, or pill/organic?
9. **Color emphasis** — brand-forward (color-led) or neutral-forward (brand as sparing accent)?
10. **Motion appetite** — static/instant, subtle/functional, or lively/expressive?

If the user can't answer, propose a default from the brand + product type, state it, move on.

## Mood → concrete parameters

Everything still resolves to **existing tokens/primitives** — this decides *which* to lean on, never new values.

| Signal | Density | Radius | Type | Color emphasis | Elevation | Motion |
|---|---|---|---|---|---|---|
| **Calm / editorial / premium** | Airy, wide margins | Small–medium | Larger type, high size contrast, generous line-height | Neutral-forward; brand a quiet accent | Low, soft | Slow-ish 250–300ms, gentle |
| **Bold / energetic / modern** | Balanced, confident blocks | Medium | Big headings, strong weight contrast | Brand-forward | Medium, crisp | Snappy 150–200ms |
| **Warm / friendly** | Balanced, comfortable | Medium–large rounded | Humanist, softer contrast | Brand + friendly accent | Soft, low | Gentle, subtle spring |
| **Precise / technical / dense** | Compact, tight | Small / sharp | Smaller, tabular figures | Neutral-forward; semantic colors do the talking | Minimal/flat, hairline | Instant–fast 100–150ms |
| **Trustworthy / institutional** | Balanced, strong grid | Small–medium | Classic hierarchy, sober weights | Neutral + brand primary only | Low, understated | Subtle, unshowy |

Read down the columns for the chosen adjectives; reconcile conflicts toward the top adjective. Record the
parameters in the plan.

## Divergence levers (for alternatives)

When prior directions exist, differ **structurally**, not just chromatically. Change **at least the nav
pattern + one of {composition, density, type personality}**:
- **Nav** — left sidebar · top bar · top bar + sub-nav · rail + drawer · bottom tab bar · sectioned hub.
- **Composition** — centered/contained · full-bleed · asymmetric/editorial · split-pane · card-grid canvas.
- **Density** — airy · balanced · compact. **Shape** — sharp · rounded · pill.
- **Type** — large-expressive · restrained-systematic · humanist · technical/tabular.
- **Color emphasis** — neutral-forward · brand-forward. **Elevation** — flat/bordered · softly layered · pronounced.
- **Motion** — instant/functional · subtle/gentle · lively/expressive.

Keep all within the same tokens and brand — divergence is in arrangement and emphasis, never off-brand values.

## Craft rules (every screen)
- **Restraint** — mostly neutral surfaces + one brand primary; accents rare and purposeful.
- **Space is a feature** — generous whitespace and consistent density read premium; crowding reads cheap.
- **Type carries hierarchy** — let the scale, not boxes and lines, do the work.
- **Every decision ladders to the thesis** — if a choice doesn't express the concept, cut it.
- **Real content, real truth** — realistic labels/names/numbers/dates; they expose problems lorem hides.
- **Bind, don't hardcode** — a look is only credible if it's achievable from the system; binding proves it.
- **Consistency is non-negotiable** — the same role looks identical on every screen.

## Anti-patterns
- Chasing a trend (glass everywhere, heavy gradients, oversized shadows) that fights the brand.
- More than one primary accent competing for attention.
- Decoration with no job — if removing it loses no meaning, remove it.
- Copying a reference pixel-for-pixel — translate its *principle* into this brand's tokens.

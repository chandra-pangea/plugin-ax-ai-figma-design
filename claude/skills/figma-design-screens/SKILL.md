---
name: figma-design-screens
description: Design complete, production-grade screens in Figma via the Figma MCP — full layouts, navigation, information architecture, real content, every applicable experience state (default, empty, loading, invalid input, error, success, failure, permission-denied, screen-specific), and motion wired as a Figma prototype. Assembles screens from an existing design system (tokens + component library) in a coherent visual direction; where no system exists yet it can establish a design direction (key screens bound to primitives). Use when the user asks to "design these screens in Figma", "build the UI for this feature", "create the screens and their states", "roll out the visual design", "design a direction / key screens / look and feel", or "design the screen and its states". Pure how-to: it designs in Figma; it writes no spec, VD contract, PRD, or ticket.
metadata:
  version: "1.0"
---

# Design Screens → production UI in Figma

Turn requirements into **complete visual designs**: full screens with their layout, navigation, IA, real
content, every applicable state, and motion — assembled from the design system in a coherent visual direction.

```
tokens (figma-design-tokens) → components (figma-design-components) → THIS skill (screens)
```

A screen is **not** a pretty happy path. It's done when a developer could build it and a user could never
reach a state that was never drawn.

## Two modes — detect first

- **Rollout (default).** A design system (tokens + component library) already exists in the file. Assemble
  screens from component **instances**, bound to semantic/component tokens, in the existing visual direction.
- **Direction (bootstrap).** No component library yet, only primitive tokens. Design a small set of **key
  screens** (entry, dashboard, main nav + layout, one complex journey) as flat frames bound to **primitive
  variables**, to establish a visual personality — then the component library and full rollout follow. Run it
  a few times for distinct alternatives to choose from.

Detect by inventorying the file (Phase 0). Everything below is written for Rollout unless marked **[Direction]**.

## Hard guardrails (read first)

1. **Bind to tokens; never hardcode.** Every fill, text color, stroke, spacing, padding, gap, radius, border,
   shadow and type property is bound to a variable. **Rollout:** a `Semantics`/`ComponentTokens` token or a
   `DS/*` style (binding a raw primitive is a defect). **[Direction]:** a **primitive** variable (no semantic
   layer exists yet). If a needed value has no variable, **stop and flag it** — never invent one.
2. **Reuse before variant, variant before new.** **Rollout:** (a) use the existing component; (b) if it can't
   express the need, add a **variant** to the existing set; (c) only then a new component. Every DS element is a
   live **instance** — never a copy, never detached, overridden only through component properties. Anything
   reusable you create lives in the component library (`DS_Core_*`), not on the screen page. **[Direction]:**
   build repeats by copying one perfected base frame; the only components are minimal page-local ones for motion.
3. **One primary action per screen.** If two things compete for primacy, fix the hierarchy — don't add a color.
4. **Every applicable state is drawn.** Walk the canonical state set (`references/states-and-motion.md`) for
   every screen; each state is a full-screen frame or recorded `N/A — affirmed — <reason>`. Silently omitting a
   state is the most common failure.
5. **Motion is built, not described.** Wire the required transitions and interactions as Figma **prototype
   reactions** (`setReactionsAsync`), using the look's motion character and the `motion/*` token values.
6. **If wireframes/mockups are given, match their structure** — elevate fidelity, don't redesign the flow.
   Precedence: tokens/components > direction > wireframe. Surface any wireframe↔requirement gap to the user.
7. **No dark mode / extra themes** unless asked.
8. **`use_figma` is the only write path.** Load `figma-use` and `figma-generate-design` before any `use_figma`
   call (add `figma-generate-library` when creating a component). Scripts are atomic — on error, read it, fix,
   retry. Build one screen/region at a time and screenshot after each.
9. **Confirm before building.** Present the plan (Phase 4) and get explicit approval. Then decide routine
   design questions yourself; don't re-ask what you're qualified to answer.
10. **Touch only your screen page(s)** (and, through the reuse ladder, the `DS_Core_*` pages). Never modify
    tokens' values or another run's screens.

## Required inputs — validate first, ask and stop if missing

1. **Figma file or page link** — page given → build there; file given (or no page) → create a page
   (`Rollout_<slug>`, or `Design_Direction_<name>` in Direction mode).
2. **The design system** — **Rollout:** verify the token collections (`Semantics`/`ComponentTokens`) and the
   component library (`DS_Core_*`) exist by inventorying the actual variables and component sets; if only
   primitives exist, you're in **[Direction]** mode. If nothing exists, route to `figma-design-tokens`.
3. **A visual direction** — **Rollout:** the existing direction's screens / the look the components express
   (screenshot them and extract nav model, grid, density, type usage, color roles, elevation, radius, icon
   family, motion). **[Direction]:** the design inputs (references, mood, personality) — see
   `references/design-craft.md`.
4. **Requirement context** — a BRD/PRD, requirements text, or a per-screen brief describing what to design.

If any is missing, ask (clean prompt) and stop. Don't substitute an assumption.

## Workflow

### Phase 0 — Inputs, mode & discovery
Detect the mode by inventorying: token collections (primitive vs semantic vs component), `DS/*` styles,
component sets, direction pages, existing screen pages. Resolve the target page; state it back. If a ticket id
was given and a tracker MCP is connected, fetch it and fold its description/criteria into the requirements.

### Phase 1 — Absorb the system & direction (never skip)
- **Tokens** (Rollout): list the semantic + component tokens by role — this is your palette; nothing outside
  it appears on a screen. **[Direction]:** list the primitive variables; flag gaps that constrain design.
- **Components** (Rollout): enumerate every component set with its variant properties — your parts bin. Note
  the nav components and layout templates; every screen starts from a template, not an empty frame.
- **Direction**: screenshot the look's screens; extract its binding rules (nav model, grid + container widths,
  density, type usage, color roles, elevation, radius, icon family, motion character).

### Phase 2 — Analyze the requirements (like a senior product designer)
Per screen derive: purpose, the one primary action, entry/exit, the real data shown, personas/permissions that
differ, and the failure modes it must survive. **Hunt for conflicts** (requirements that contradict each other
or the system/direction; a wireframe diverging from the requirement; an acceptance criterion with no surface; a
flow step with no screen) and put them to the user — never design over a conflict. Ask only the questions only
the user can answer (business rules, data volumes, terminology), batched, each with your recommended answer.
Screen set: if the user named the screens, that list is final; else derive the minimum set and state it back.

### Phase 3 — Gap analysis (Rollout) / mood & divergence ([Direction])
- **Rollout:** walk each screen's anatomy against the parts bin; classify each need (existing component / new
  variant / new component / existing token / new token-alias / stop-and-add-primitive) per
  `references/gap-protocol.md`. Build any additions **in the component library** before the screens that use
  them.
- **[Direction]:** translate the design inputs into concrete parameters (density, radius personality, type
  character, color emphasis, elevation, motion) via `references/design-craft.md`; if prior directions exist,
  pick a deliberately **distinct** concept (nav, composition, density, type, shape, color emphasis, motion).

### Phase 4 — Plan (approve before building)
One compact plan per screen: name, purpose, primary action, the template it starts from, where it sits in nav
& IA; layout (regions, grid, content hierarchy top-to-bottom); real content + the extremes it must survive;
**states** (the canonical walk — each *drawn* or `N/A — affirmed`); components used + any Phase 3 additions;
motion to wire; and any design decision you made on the user's behalf, one line each. Show it, get approval.

### Phase 5 — Build the screens
Follow `references/figma-build.md`: one **section per screen**, default frame first (`<NN> · <Screen>`), then
one frame per state left-to-right in canonical order. **Rollout:** start from a template instance, compose with
component instances, bind every remaining property to a token. **[Direction]:** flat auto-layout frames bound
to primitives; copy one perfected base frame for repeats. Real content throughout. Build one screen at a time:
assemble → screenshot → validate bindings/instances → fix → next.

### Phase 6 — Build the states
Every planned state frame is a **complete screen** (full shell, state applied to the affected region), using
the components' own state variants (`disabled`, `error`, `loading`, `empty`) — never a restyled default. Follow
`references/states-and-motion.md` for what each state must show.

### Phase 7 — Motion & prototype
Follow `references/states-and-motion.md` §Motion. Wire screen-to-screen navigation, overlay open/close
(modals/drawers/menus/toasts), in-screen state transitions (default→loading→filled; submit→success|failure),
and feedback on primary controls — as Figma prototype reactions, durations/easing from the `motion/*` token
values. Record reduced-motion / interruptibility as handoff notes (a prototype can't express them).

### Phase 8 — Self-critique, audits & accessibility
Screenshot every frame and run `references/screen-checklist.md` in full (per-screen, per-set, motion), plus
**WCAG AA** contrast on every text/bg and UI/bg pair **in every state** (hover, disabled, error). Run the two
scripted audits (`references/figma-build.md` §Audits): raw-value/primitive-binding, and instance integrity. Run
the **nativeness check** (new screen beside a direction screen and a prior screen — any seam is a defect). Fix
everything and re-check. Report honestly, including accepted trade-offs and any unresolved conflicts.

## The craft
- Design the states and the screen designs itself. The empty state is where a product is won.
- Type carries hierarchy; reach for size, weight and space before borders and fills.
- Space is a feature; restraint with color (neutral surfaces, one primary, rare purposeful accents).
- Real content, real truth — realistic names, numbers, dates, and the long ones. Lorem hides layout bugs.
- Reuse is a design decision: a screen assembled from the system is consistent by construction.
- A new screen should look like it was always there.

## Reference files
- `references/design-craft.md` — the look-and-feel interview, mood→parameter mapping, divergence levers, craft rules.
- `references/gap-protocol.md` — the reuse ladder and how to grow the system without corrupting it (Rollout).
- `references/states-and-motion.md` — the canonical state set and the motion/prototype patterns.
- `references/figma-build.md` — `use_figma` patterns: page/section/frame conventions, template-first assembly, binding, prototype reactions, tagging, the two audits.
- `references/screen-checklist.md` — the Phase 8 quality bar.

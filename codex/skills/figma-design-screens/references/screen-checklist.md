# Screen quality checklist (Phase 8)

> Part of `figma-design-screens`. Run in three passes: **A) per screen** (default *and* every state frame),
> **B) per set** once across the run, **C) motion** against the prototype. Ships only when every item passes or
> a trade-off is explicitly accepted and recorded. Prefer inspecting bindings over eyeballing — a color that
> "looks right" but is hardcoded fails A1.

## How to run it
1. `get_screenshot` every frame — default and every state.
2. Walk A1–A9 against each screenshot and the frame structure (auto-layout, instances, bound variables).
3. Run the two scripted audits (`figma-build.md` §Audits). These are gates, not opinions.
4. Walk B10–B14 once across the run, and C15–C17 against the prototype.
5. **WCAG AA** on every text/bg and UI/bg pair, in **every state** (hover, focus, disabled, error).
6. List every violation as *frame · item · fix*. Fix, re-screenshot, re-check until clean.

## A · Per-screen (every frame, including every state)

### A1. System fidelity — gate
- [ ] Every fill, text color, stroke, spacing, padding, gap, radius, border and shadow is bound to a token
      (Rollout: semantic/component token or `DS/*` style; Direction: a primitive variable). No raw values.
- [ ] (Rollout) No binding to a primitive; every DS element is a live **instance** (no detached, no hand-drawn
      lookalike); instance customization via component properties/variants only.
- [ ] (Rollout) Anything reusable created this run lives in the component library, not on the screen page.

### A2. Layout & structure
- [ ] (Rollout) Starts from the DS template/app-shell; nav + shell are instances, identical to every screen.
- [ ] Every frame is auto-layout; nothing free-floating except deliberate overlays.
- [ ] Content respects the look's grid, column count and container max-width; shared edges and numeric data align.
- [ ] All spacing from spacing tokens — no eyeballed gaps. Density matches the look's stated density.

### A3. Visual hierarchy
- [ ] One unmistakable focal point, graspable in under 3 seconds.
- [ ] Exactly **one primary action**, the most prominent control; secondary/tertiary clearly subordinate.
- [ ] Reading order flows by deliberate size/weight/color/space. Related elements grouped; unrelated separated.

### A4. Cognitive load
- [ ] No unexplained jargon, no raw IDs where a name exists, no computation left to the user.
- [ ] ≤ ~7 ungrouped sibling choices in a cluster/menu; beyond that, group or progressively disclose.
- [ ] Advanced/rare options disclosed progressively. Every field/column/control is required by a requirement.

### A5. Typography
- [ ] Only the type tokens / `DS/*` styles — no off-scale sizes/weights/line-heights.
- [ ] Few-step hierarchy with obvious jumps; few styles per screen; body measure ~50–75 chars.
- [ ] Truncation/wrap behavior decided and shown wherever content can overflow.

### A6. Color, contrast & meaning
- [ ] The same role uses the same token everywhere.
- [ ] Surfaces form a legible value hierarchy (page < surface < raised/overlay) from surface tokens.
- [ ] Body text ≥ 4.5:1; large text/icons/meaningful UI ≥ 3:1 — in **every state**.
- [ ] Status/meaning never color-only (paired with icon/label/shape); semantic color reserved for meaning.

### A7. Interactive elements & accessibility
- [ ] Every interactive element shows default/hover/focus-visible/active/disabled (inputs also error) via the
      component's state variants.
- [ ] Focus visibly indicated with a real treatment (ring/outline), not just a color shift.
- [ ] Hit targets ≥ 44px touch, ≥ 32px pointer, with separation. Destructive actions distinct, not adjacent to
      the primary, confirmed before commit. Disabled controls explained.
- [ ] Intended keyboard/focus order coherent with the visual order. No orphan interaction (every control leads
      somewhere in the prototype or is marked non-functional-in-this-run).

### A8. Content realism & resilience
- [ ] Real, plausible domain content — no lorem, no "Title goes here". Numbers/dates/currency formatted plausibly.
- [ ] Layout survives extremes: longest string, shortest value, zero/one/many — with stated truncation/wrap.

### A9. State completeness
- [ ] Every canonical state drawn as its own full-screen frame or recorded `N/A — affirmed` with a reason.
- [ ] Each state frame is a complete screen; empty states name the cause + way out; error states name the
      error, reason and recovery; permission-denied says what's denied and who grants it.
- [ ] One loading treatment across the whole product. State frames reuse the component's state variants.

## B · Per-set (once, across the run)

### B10. Direction conformance
- [ ] Beside a direction screen, a new screen shows **no seam** (nav, grid, density, type, color roles, radius,
      elevation, icon family). One icon family at consistent size/stroke, labeled where meaning isn't obvious.
- [ ] Elevation signals hierarchy from the scale (raised/overlay/modal), flat where flat reads cleaner.

### B11. Cross-screen consistency
- [ ] The same pattern solved the same way on every screen (headers, toolbars, tables, forms, empty states).
- [ ] Terminology identical across screens for the same concept. New screens indistinguishable from existing ones.

### B12. Navigation & IA
- [ ] The look's nav model present and consistent; the user can always tell where they are.
- [ ] Every screen is reachable; every outbound path leads to something that exists or is marked out of scope.
- [ ] IA matches the user's mental model, not the schema. Overlays vs pages is a deliberate choice.

### B13. Design-system integrity (Rollout)
- [ ] Every DS addition followed the gap protocol (built in the library, token-bound, documented, tagged).
- [ ] No addition duplicates something the system already had under another name.

### B14. Requirement coverage
- [ ] Every in-scope requirement has a surface, traceable to a screen (and state).
- [ ] Every in-scope acceptance criterion is satisfiable on the drawn screens.
- [ ] Every Phase-2 conflict is resolved or recorded as an open question — none silently designed over.

## C · Motion (the prototype)

### C15. Present & purposeful
- [ ] Every animation communicates a state change — none decoration.
- [ ] The required set is wired: navigation, overlay open/close, in-screen state transitions, primary-control feedback.
- [ ] Motion subtle (fade / small slide / small scale); only the changing element animates.

### C16. On-system
- [ ] Durations/easing from the `motion/*` token values and the look's character; most transitions 100–300ms.
- [ ] Easing everywhere (never linear); same element type animates the same way; Smart Animate pairs share layer names.

### C17. Prototype integrity
- [ ] Overlays wired as overlay interactions, not page swaps.
- [ ] The prototype plays end to end for the in-scope flows; no dead hotspot, no trigger to a deleted frame.
- [ ] Reduced-motion and interruptibility recorded as handoff notes — not faked in the prototype.

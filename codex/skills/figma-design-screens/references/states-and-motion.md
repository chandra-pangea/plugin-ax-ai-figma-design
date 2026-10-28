# States & motion

> Part of `figma-design-screens`. Phase 4 plans states from here, Phase 6 draws them, Phase 7 wires motion.

## Part 1 · The canonical state set

Walk this for **every** screen. Each state is either **drawn as its own full-screen frame** or written
`N/A — affirmed — <reason>` in the plan. Never dropped silently.

| State | Applies when | What the frame must show |
|---|---|---|
| **Default / filled** | Always | The screen doing its job with real, representative data |
| **Empty — no data yet** | Shows a collection the user can create into | Why it's empty, what the thing is, the primary way to create the first one. Never a bare "No data." |
| **Empty — no results** | Has search/filter | That the filter (not the data) caused it, which filter, a one-click way to clear it |
| **Loading — initial** | Data is fetched | The product's single loading treatment (skeleton *or* spinner — one rule everywhere), shell already in place so layout doesn't jump |
| **Loading — in-place / submitting** | An action is in flight | The affected control busy, the rest readable; destructive actions disabled while in flight |
| **Invalid input** | Any form field | Field-level messages at the prescribed position, on the offending fields; the submit affordance's state explicit |
| **Validation / business-rule error** | Rules beyond field format | The rule that failed in plain language, where it applies, what the user can do |
| **Submission success** | A write completes | The confirmation (inline/toast/state change), the new truth on screen, the next step |
| **Submission failure** | A write fails | What failed, whether data was kept, a retry. Never a raw error code alone |
| **Permission denied / read-only** | Personas differ in access | What is denied, what the user *can* still do, who to ask. Read-only shows data without the actions |
| **Partial / degraded** | A dependency can be down | Which part is unavailable, that the rest works, whether data shown is stale |
| **Long-running / background** | Work continues past the request | Progress/status, whether the user can leave, how they'll be told it's done |
| **Screen-specific** | Domain states the requirements imply | blocked, expired, archived, over-quota, conflict, first-run, offline |

**Rules**
- A state frame is a **complete screen** (full shell with the state applied), never a cropped fragment.
- States use the components' own **state variants** (`disabled`, `error`, `loading`, `empty`) — never a
  restyled default.
- The same state looks the same on every screen. Empty/loading/error are product-wide patterns.
- If a state's behavior isn't knowable from the requirements, **ask** — it's a requirement gap, not a design choice.

## Part 2 · Motion

Motion is built as Figma **prototype reactions** via `use_figma` (`setReactionsAsync`) so it plays in Figma.
Load `figma-use` for the reactions API; add `figma-use-motion` only for in-node timeline micro-animations.

### What to wire (the required set)
1. **Screen-to-screen navigation** — from the nav instance to each screen, using the look's page-transition character.
2. **Overlay open/close** — modals/drawers/menus/popovers/toasts as overlay interactions
   (`OPEN_OVERLAY`/`CLOSE_OVERLAY`), with the look's entry/exit and a backdrop treatment.
3. **In-screen state transitions** — state frames connected in experience order: default→loading→filled;
   submit→loading→success|failure; empty→created.
4. **Interaction feedback** — primary controls' hover/pressed/focus, via **Smart Animate** between the
   component's own variants.

### How it should feel
- **Purpose only** — every animation communicates a state change; decoration is a defect.
- **Fast** — most UI transitions 100–300ms; overlays/page transitions at the longer end, feedback at the shorter.
- **Eased, never linear** — ease-out for entrances, ease-in-out between states, from the `motion/easing/*` values.
- **Tokenized** — read the `motion/duration/*` and `motion/easing/*` variable values and apply those numbers
  (duration in seconds in the reaction). Same element type → same speed everywhere.
- **Subtle** — fade, small slide, small scale. No bounce/spin/overshoot unless the look established it.
- **Only the changing element moves.** Spatially continuous — things enter from / exit to logical origins.
  Smart Animate matches by **layer name**; keep names identical across animated frames or it cross-fades.

### [Direction] mode — motion sample
When establishing a look, demonstrate the motion language on a **representative sample**, not everything: a
button (default→hover→pressed), a nav interaction (hover + active, and screen-to-screen), one popup/modal
(open/close), and the complex journey's transitions. Build the interactive elements once as **page-local,
unpublished** components with one variant per state and Smart-Animate between them.

### What cannot be built here
Reduced-motion and interruptible/non-blocking behavior can't be expressed in a Figma prototype — record them as
handoff notes (every animation skippable under `prefers-reduced-motion`; no animation blocks input). Don't fake them.

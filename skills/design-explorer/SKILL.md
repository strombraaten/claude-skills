---
name: design-explorer
description: >
  Multi-direction UI design exploration tool. Generates 6–8 completely different design mockups
  as static HTML previews and displays them in a browsable carousel gallery with voting, notes,
  and feedback synthesis — so you can compare directions instead of reacting to one thing at a time.
  Use this skill whenever the user wants to explore UI design options, generate multiple design
  directions, mockup a screen, iterate on visual design, or says things like "show me some design
  options", "generate some UI mockups", "I want to explore how this could look", or "let's figure
  out the design for X". Also use it when the user is stuck in a design feedback loop or wants to
  break out of a single-direction approach. This is the right skill whenever design exploration,
  visual iteration, or UI direction-finding is the goal — even if the user just says "I want to
  see what this could look like."
---

# Design Explorer

Help the user rapidly explore multiple distinct UI design directions for a screen or component.
The core insight: comparing directions ("I want the warmth of #2 but the layout of #5") is far
more productive than reacting to one mockup at a time.

## Phase 1 — Gather Context

Start with a focused conversation to understand the design target. Ask:
- What specific screen or component are we designing? (e.g., "reading journal entry view",
  "dashboard overview", "onboarding flow")
- What's the app/project? Can you point me to the codebase or describe it briefly?
- Are there any hard constraints? (mobile-only, must match existing brand, etc.)
- How many directions would you like? (default: 6)

Then **read the project codebase** to find:
- Existing color palette and CSS variables (look for `:root` blocks, Tailwind config, CSS files)
- Real data schemas and field names (look at types, state objects, API shapes)
- Typography choices (font families in use)
- Any existing component patterns or UI library in use

Use this real project context in the mockups. If you can't find a codebase, ask the user to
describe the data model and color preferences briefly.

## Phase 2 — Generate the Mockups

Generate 6 (or the requested number) completely distinct HTML mockups. Each should represent
a fundamentally different design philosophy — not just color variations.

**What "genuinely different" means:**
- Different layout paradigms (single-column centered vs. sidebar+content vs. full-bleed cards)
- Different visual registers (warm+editorial vs. data-dense+utilitarian vs. playful+illustration)
- Different information hierarchies (what's most prominent varies across designs)
- Different interaction models (inline editing vs. modal vs. separate view vs. bottom sheet)

**Suggested direction archetypes to draw from:**
1. Editorial / magazine-style (generous whitespace, large typography)
2. Dashboard / data-dense (compact, lots of info visible at once)
3. Card-based / spatial (floating cards, elevation, shadows)
4. Minimalist / monochrome (one accent color, high contrast)
5. Warm / human (rounded corners, warm tones, handwritten-feeling elements)
6. Dark / premium (dark background, glowing accents)
7. Mobile-native / gesture-forward (bottom navigation, swipe affordances)
8. Brutalist / bold (strong borders, grid-heavy, unapologetically structured)

Pick 6 that feel genuinely different from each other given the context.

**Technical rules for each mockup file:**
- File naming: `design-1.html`, `design-2.html`, etc.
- Each file is fully self-contained: all CSS inline or in `<style>` tags
- External dependencies OK: Google Fonts (link tag), no JS libraries
- Use the project's real field names and realistic data values (not Lorem ipsum)
- Each mockup should have a clear `<!-- Design Name: [descriptive name] -->` comment at the top
  and an `<h1>` or visually prominent title showing the design name (e.g., "Direction 2: Warm Editorial")
- Target a single screen/state — don't try to show the whole app flow
- Make it look complete and polished — this isn't a wireframe

**Write all mockups to a directory called `_design-exploration/`** in the project root (or the
closest sensible location). If no project root is clear, ask the user where to save them.

## Phase 3 — Create the Gallery Viewer

Read the gallery template from `assets/gallery_template.html` (in this skill's directory).
Replace the placeholder `__DESIGNS_JSON__` with a JSON array of objects, one per design:

```json
[
  { "file": "design-1.html", "name": "Direction 1: Warm Editorial" },
  { "file": "design-2.html", "name": "Direction 2: Dark Premium" }
]
```

Save the result as `_design-exploration/_gallery.html`.

The gallery handles all browsing, voting, and feedback collection — you don't need to write
any gallery logic yourself.

## Phase 4 — Start the Preview

Use `preview_start` (from the Claude Preview MCP) to launch a local server pointing at the
`_design-exploration/` directory. Open `_gallery.html` as the entry point.

Tell the user:
> "I've generated [N] design directions and opened the gallery. Browse with ← → arrow keys,
> vote each design up or down, and add notes. When you've seen them all, click 'Summarize
> Feedback' and paste the summary back here — or just describe what you liked and disliked
> in your own words."

## Phase 5 — Synthesize Feedback and Iterate

When the user returns with feedback:

1. **Extract the signal.** Look for comparative statements ("the layout of #3 but the color
   palette of #5"), strong reactions (explicit loves or hates), and recurring themes.

2. **Synthesize into a design brief.** Summarize: what layout direction won, what visual tone
   resonated, what to avoid, and what specific elements to carry forward.

3. **Offer the user a choice:**
   - **Refine**: Generate a new round of 3–4 designs that explore the winning direction more
     deeply, applying the feedback. Repeat from Phase 2 with narrower brief.
   - **Converge**: Generate 1–2 "synthesis" designs that combine the best elements from
     multiple directions.
   - **Handoff**: Help convert the winning mockup into production-ready code using the actual
     framework/component library from the project.

For the handoff path, read the winning `design-N.html` carefully and translate it to the
project's actual stack (React components, Tailwind classes, ShadCN components, etc.).

## Tips for Writing Great Mockups

- Spend the most effort making each design feel *complete* — real labels, real numbers, real
  copy. A mockup that looks like a real app is 10x more useful than a careful wireframe.
- Vary the mockups along axes the user actually cares about: information density, warmth,
  modernity, mobile-friendliness. If they mentioned "I want something clean", skew the
  archetypes accordingly but still give them real variety.
- Don't be afraid of bold choices. The user can vote them down. A surprising direction often
  unlocks a conversation that a safe one doesn't.
- If generating a second round, explicitly call out what you took from each previous direction
  in a comment at the top of each new file.

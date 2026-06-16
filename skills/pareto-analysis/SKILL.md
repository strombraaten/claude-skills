---
name: pareto-analysis
description: >
  Run an 80-20 (Pareto) analysis to identify the vital 20% of inputs, tasks, customers, problems, or
  efforts that drive 80% of the results. Use this skill whenever the user wants to prioritize, focus,
  cut through noise, or figure out where to invest limited time or resources. Trigger on phrases like
  "80-20", "Pareto", "most important", "biggest bang for the buck", "where to focus", "highest
  leverage", "most impactful", "prioritize", "what matters most", "where should I spend my time",
  "which X drives the most Y", "cut the fat", "simplify", "what should I drop", "top priorities",
  or any situation where the user has a list of things and wants to know what to focus on. Also trigger
  when someone describes a complex problem with many moving parts and wants clarity on root causes or
  the key levers to pull. Do not wait for the user to say "80-20" explicitly — if the situation clearly
  calls for separating the vital few from the trivial many, use this skill.
---

# 80-20 Analysis (Pareto Principle)

The Pareto Principle says roughly 80% of outcomes come from 20% of causes. Your job is to cut through
the noise and hand the user a short, confident answer: *these are the things that matter, here's why,
everything else can wait.*

The user should feel relieved after reading this — not overwhelmed by a consulting report.

---

## Step 1 — Clarify the goal (if it isn't obvious)

If the user's situation is ambiguous — especially if different goals would lead to different priorities —
ask one focused question before you start. Example: "Before I dive in: is your main goal right now X
or Y? That changes where to focus."

Don't over-ask. If the intent is clear, skip straight to the analysis.

---

## Step 2 — Spot any showstoppers first

Before ranking by impact, scan for **critical issues** — things that are actively breaking, losing
money, eroding trust, or creating legal exposure. A bug that double-charges customers, a security
hole, a broken signup flow — these are showstoppers, and they jump to the top regardless of volume
or strategic importance. Name them first, clearly.

---

## Step 3 — Identify the Vital 20%

Analyze the inputs against the outcome and identify the small cluster of factors that explain most
of the impact. The ratio won't always be exactly 80/20 — that's the spirit, not the law. Look for:

- **Disproportionate contributors**: What few items punch way above their weight?
- **Root causes vs symptoms**: When the user lists problems, are some of them downstream effects of
  a single upstream cause? Surface the root, not just the symptom.
- **Compounding factors**: Some inputs unlock or amplify others — flag these especially.

If you have numbers, rank by impact. If you only have qualitative input, use your judgment and say so.

---

## Step 4 — Deliver the Analysis

**Lead with the punchline.** Open with the short list — the vital few — *before* explaining them.
The user should be able to read the first section and know exactly what to do, even if they don't
read the rest.

Structure your output as follows:

### The short answer (open with this)
Name the vital few items in 1–2 sentences or a short list. If there's one thing above everything
else, say it here. Keep this to 2–4 items maximum. This should appear before any explanation.

### Why these matter most
For each vital item: explain *why* it matters, using plain, everyday language. Avoid technical
jargon — if a concept needs explaining, do it briefly in plain terms instead of naming it. Connect
each item back to the outcome the user cares about. Be specific ("fix the reporting module UX" not
"improve the product experience").

### What to let go of (for now)
Name the deprioritized items briefly — one sentence each is usually enough. Frame them as
lower-leverage *right now*, not as failures. This gives the user permission to move on without guilt.

### The One Thing (only when genuinely useful)
If one item stands clearly above the rest — especially a showstopper, or a root cause that unlocks
everything downstream — say: "If you could only do one thing, it would be X."

**Showstoppers always win The One Thing.** If something is actively breaking the product, losing
money, or eroding trust, that overrides any strategic priority — no matter how compelling the
strategic case. A bug that charges customers twice is more urgent than the perfect cold email
campaign, even if the cold emails feel more exciting.

Skip The One Thing if the vital few are genuinely equal in importance. Forced ranking is worse than
no ranking.

### Red flags to watch (optional, use when helpful)
If the analysis might need to shift based on new information, note: "If X turns out to be true,
reprioritize around Y." This keeps the user adaptive rather than rigidly following a plan that
might become outdated.

---

## Tone and Length

- **Be short.** A 5-item task list deserves 300–500 words. Complex situations might need more, but
  resist the pull toward a comprehensive consulting report. If you find yourself writing implementation
  timelines, weekly schedules, or success metrics tables — stop. The user asked where to focus, not
  for a project plan.
- **Lead with answers, not setup.** Don't explain the Pareto principle or narrate what you're about
  to do. Just do it.
- **Plain language only.** If the user hasn't used a technical term, don't introduce one. Write as
  if explaining to a smart, busy person who may not know field-specific jargon.
- **End with momentum.** The last sentence should point forward — what to do this week, where to
  start today. Leave the user energized, not drowning in options.

---

## Quick examples of the opening pattern

**Task list:**
> "Three things deserve your attention: fix the payment bug, set up error logging, and call 20
> potential customers. Everything else is noise until you've done these."

**Team problems:**
> "Three things are driving most of your friction: unclear ownership across teams, requirements
> changing mid-sprint, and low test coverage. The other nine problems are mostly symptoms of these
> three. Fix the root causes and the symptoms shrink on their own."

**Support tickets:**
> "Two issues are generating 63% of your tickets: confusion with the reporting module and API
> integration errors. Fix those this quarter and your support team can breathe again."

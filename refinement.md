# Refinement (Office-Hours Pass)

Runs in Phase 4 when `--refine` is set. Takes the top-ranked idea and pressure-tests it the way a YC partner would in office hours, before any code is written. Modeled on the gstack `/office-hours` skill. If the real gstack `/office-hours` command is installed, defer to it instead.

The goal is not to praise the idea. It is to find the real product hiding inside it and the narrowest way to prove demand.

## Step 1: Reframe

Read the validated idea and its evidence. Then push back on the framing.
- Separate the pain from the proposed feature. The user named a feature; name the pain underneath it.
- State the bigger product that pain implies, if there is one. Be direct: "You validated X, but what the evidence describes is closer to Y."
- If the idea is already correctly scoped, say so plainly and move on. Do not invent a grander version for its own sake.

## Step 2: Premise challenge

Write 3 to 5 falsifiable premises the idea depends on. Not "does this sound good," but claims that could be proven wrong. Example shape:
- "The buyer feels this pain weekly, not yearly."
- "They already pay for a worse workaround."
- "The narrowest useful version is X, and it ships in N weeks."

The user accepts, rejects, or adjusts each. Record the result. Accepted premises are load-bearing in the design doc. A rejected core premise should send the idea back, not forward.

## Step 3: The six forcing questions

Ask these one at a time. They are uncomfortable on purpose.

1. **Demand reality.** Who specifically wants this, and how do you know? Name a real person or a real thread, not a persona.
2. **Status quo.** What do they do today instead? If the answer is "nothing," demand may be weaker than it looks.
3. **Desperate specificity.** Who is the most desperate user, the one who needs this so badly they will tolerate a rough first version?
4. **Narrowest wedge.** What is the smallest thing you can build that someone would actually use this week?
5. **Observation and surprise.** What did the evidence show that you did not expect? Surprises are where the real product usually hides.
6. **Future-fit.** If this works, what does it become in two years? Does that future justify starting now?

If the user cannot answer question 1 with a specific human, stop and say so. That gap is the most valuable output of the whole run.

## Step 4: Implementation alternatives

Offer 2 to 3 concrete build approaches with honest effort estimates. Shape:
- Approach A, narrowest wedge: ships in days, proves the core premise, smallest scope.
- Approach B, fuller version: more capability, more weeks, more risk.
- Approach C, full vision: everything, only sane once A has real usage.

Recommend A in almost all cases, because real usage teaches more than planning. State the reasoning.

## Output

Write `idea-forge-design-<timestamp>.md` containing:
- The idea and its composite score from the council.
- The reframe.
- Accepted premises (and any rejected, with why).
- The six answers.
- The chosen approach and the concrete first step.

This doc is the handoff to whatever planning or build process comes next.

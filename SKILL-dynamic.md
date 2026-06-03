---
name: idea-forge-dynamic
description: Dynamic-workflow variant of Idea Forge. Runs the same ideation-swarm and validation-council pipeline as the standard version but with adaptive parallelism, inline quality gates, smart-skip logic, dynamic critic selection, and progressive output. Faster wall-clock time, lower token cost, smarter routing. Use when you want the standard skill but optimized. Trigger on "forge ideas smart:", "dynamic ideate on", "smart brainstorm", "idea swarm dynamic".
---

# Idea Forge — Dynamic Workflow

Same pipeline as `SKILL.md` — ideation swarm, source verification, validation council, optional refinement — but the orchestrator makes live routing decisions rather than following a fixed script. Phases overlap where safe, weak signals trigger adaptive deepening, and strong signals short-circuit expensive steps.

Read `SKILL.md` for shared concepts: persona descriptions, critic axes, anonymization scheme, report format, output format. This file only describes what is different.

## Triggers

- `forge ideas smart: <topic>`
- `dynamic ideate on <topic>`
- `smart brainstorm <topic>`
- `idea swarm dynamic <topic>`

## Configuration

All flags from `SKILL.md` apply. Additional dynamic-only flags:

- `--auto-mode` let the orchestrator pick lite/standard/deep based on topic complexity  [on by default in dynamic version]
- `--no-gate` disable smart-skip gates (run all phases regardless of signal strength)  [off]

## What is Different

| Capability | Standard | Dynamic |
|---|---|---|
| Verification start | After all 5 ideators finish | Per-ideator as outputs arrive |
| Synthesizer | Always runs (except lite) | Skipped if pool is already strong |
| Adaptive deepening | Never | Auto-spawns 2 extra ideators if EVIDENCED count is low |
| Mode selection | User sets | Auto-selected from topic complexity unless overridden |
| Critic selection | Fixed 5 personas | 2 mandatory + 3 chosen based on idea pool |
| Peer review start | After all 5 critics finish | After first 3 critics return |
| Deep second round | Always runs in deep mode | Skipped if top idea is strong enough |
| Output cadence | One block at the end | Progress updates after each phase |
| Token cost | Standard | Lower: compressed handoffs, no idea pool re-send to reviewers |

---

## PHASE 0, Smart Framing

Same as `SKILL.md` Phase 0 with one addition:

**Auto-mode selection** (runs unless `--mode` is explicitly set):

Assess the topic along three axes and set mode accordingly:

- Signal: is the topic specific and well-scoped? A one-to-two word topic with no context → lite. A full sentence with domain, region, or constraint detail → standard.
- Stakes: does the topic imply a major commitment ("my main product", "investing in", "leaving my job", "bet the company") → deep.
- Novelty: is this a space where evidence is hard to find (niche markets, emerging tech, underserved geographies) → standard or deep to allow extra searches.

State the chosen mode and the reason in one line before proceeding. The user can override.

---

## PHASE 1, Streaming Ideation Swarm

Spawn the ideators the same as `SKILL.md`. The key difference: **do not wait for all ideators to finish before starting verification**.

As each ideator's output arrives:
1. Immediately begin web-fetching its cited URLs (Phase 1.5 logic runs per-ideator in parallel with remaining ideators).
2. Tag ideas EVIDENCED or SPECULATIVE as verification resolves.

This means Phase 1.5 is not a separate sequential step — it runs concurrently with the tail of Phase 1. By the time the last ideator returns, most citations are already verified.

### Synthesizer skip gate

After all ideators have returned and verification is complete, assess:

- If the verified pool contains 10 or more EVIDENCED ideas with average REALISM above 7: skip the synthesizer. The pool is strong enough to go to the council directly. Note "synthesizer skipped: strong pool" in the progress output.
- Otherwise: run the synthesizer as specified in `SKILL.md`.

### Adaptive deepening gate

After verification (or concurrent with synthesizer if running):

Count EVIDENCED ideas in the pool.

- Fewer than 4 EVIDENCED ideas: the pool is thin. Spawn 2 targeted catch-up agents in parallel — Pain Hunter and Adjacent Mover — with an explicit directive: "the current pool is weak on evidence, focus on finding cited real problems." Verify their outputs immediately on return. Merge into the pool before council.
- 4 or more EVIDENCED ideas: proceed without extra agents.

Log the gate decision in the progress output.

---

## PHASE 2, Adaptive Council

### Dynamic critic selection

The 5 standard critics are: The Contrarian, First Principles, Market Realist, The Executor, The Outsider.

In the dynamic version, two critics are always spawned: **The Contrarian** and **First Principles** (they catch the largest failure modes regardless of domain). The remaining 3 are chosen based on the verified idea pool:

Assess the pool after Phase 1 and pick 3 from this extended roster based on what the ideas most need:

| Critic | When to pick |
|---|---|
| Market Realist | Any ideas with demand uncertainty or a crowded space |
| The Executor | Most ideas are SPECULATIVE or have low REALISM |
| The Outsider | Ideas are complex to explain; clarity is a risk |
| Evidence Demand | More than half the pool is SPECULATIVE; scrutiny of weak evidence needed |
| Speed Skeptic | Ideas claim fast build times; timeline assumptions need pressure |
| Domain Expert | Ideas cluster in a specific regulated or technical domain (fintech, health, legal); domain fit needs review |

State which 3 were selected and the one-line reason for each.

### Concurrent peer review

Do not wait for all 5 critics to finish before starting peer review.

Start peer review as soon as 3 critic outputs are available. Spawn the 5 peer reviewers with whatever critiques are ready, noting that 2 are pending. When the remaining critics return, spawn 2 additional reviewers covering the complete 5-critique set. The chairman waits for all 7 reviewers before synthesizing.

This saves wall-clock time on long critic runs without sacrificing coverage.

### Preliminary chairman pass

After 3 critics and initial peer review complete, produce a brief preliminary ranking in chat — 3 ideas in order with a one-line note. Label it clearly as "early look, subject to revision." This gives the user signal while the run continues.

The final chairman pass runs after all critics and all reviewers complete, same as `SKILL.md`.

---

## PHASE 2.5, Smart Skip Gate

Before running the deep second round, check:

- If the top-ranked idea has composite score above 8.5 AND is tagged EVIDENCED: skip Phase 2.5. Note "deep second round skipped: strong lead idea" in the output and proceed to Phase 3.
- Override with `--no-gate` if the user wants the full run regardless.

If Phase 2.5 runs, follow `SKILL.md` Phase 2.5 exactly.

---

## PHASE 3, Progressive Output

The dynamic version emits progress updates throughout the run rather than waiting until the end:

- After Phase 1 ideators complete: "Phase 1 done. X ideas collected (Y evidenced). Verification running."
- After adaptive deepening gate: "Gate check: [passed / deepening triggered — 2 extra agents spawned]."
- After verification: "Verification done. X ideas proceed to council (Y evidenced, Z speculative, N merged as duplicates)."
- After preliminary chairman: show early look shortlist.
- After final chairman: show the final ranked shortlist and offer to save.

Final output format and save behavior follow `SKILL.md` Phase 3 exactly (in-memory by default, `--save` to write files).

---

## PHASE 4, Office-Hours Refinement

Identical to `SKILL.md` Phase 4. No changes.

---

## Token Budget

The dynamic version reduces token load through four specific choices:

1. **Compressed idea handoffs to the council**: summarize each idea to 150 words (title, problem, evidence, REALISM, TAG) before injecting into the critic prompt. The full text is kept in the orchestrator's context for the transcript.
2. **No idea pool re-send to peer reviewers**: reviewers receive idea titles only, not the full schema (same as standard version's fix).
3. **Synthesizer skipped on strong pools**: saves one agent call when unnecessary.
4. **Smart-skip gate on Phase 2.5**: saves 5 to 10 agent calls on strong deep-mode runs.

---

## Notes

- All security rules from `SKILL.md` apply: untrusted web fetch, HTML-escape in reports, topic content wrapped in delimiters.
- All output files are in-memory by default; `--save` to persist.
- If a gate fires unexpectedly (e.g., deepening gate fires on a topic that should have plenty of evidence), note it in the progress output so the user understands what happened.
- The council still exists to tell the user which ideas are weak. The dynamic version does not soften this — it only routes faster.

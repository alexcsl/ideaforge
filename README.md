# Idea Forge

Turn a topic into a ranked, validated shortlist of ideas, with the sources checked and the weak ideas killed. A Claude Code skill that works in any language and region.

You give it a direction. It runs a swarm of researcher agents that find real problems on the web and in regional communities, proposes ideas, verifies every cited source, then puts the pool through an adversarial council that scores and ranks the survivors. Optionally it pressure-tests the top idea in a YC-style office-hours pass. You get a ranked shortlist, a visual HTML report, and a transcript.

## Why it exists

A single model asked for ideas is agreeable and ungrounded. It will invent demand that does not exist and praise whatever you propose. Idea Forge fixes both halves: the swarm forces ideas to point at observed, cited problems, and the council is built to tell you which ideas are weak. Sources are fetched and verified, so a fabricated citation gets caught and the idea is downgraded.

## What you get

- A ranked shortlist in chat: title, composite score, one-line rationale, first step.
- A self-contained HTML report (see `examples/sample-report.html` for a real one).
- A full markdown transcript of every agent, critique, and verdict.
- A running kill-log that stops future runs from resurfacing the same dead ends.

See the rendered example without installing anything: open `examples/sample-report.html` in a browser.

## How it works

1. **Framing.** Detect language and region, load the kill-log, state the constraints.
2. **Ideation swarm.** Five persona agents plus a synthesizer search the web and region-appropriate communities in parallel and propose ideas, each tagged EVIDENCED or SPECULATIVE.
3. **Source verification.** Every cited URL is fetched. Dead or unrelated links downgrade the idea to SPECULATIVE.
4. **Validation council.** Five critics score each idea on its own axis, peer-review each other anonymously, and a chairman ranks the survivors.
5. **Deep second round (deep mode only).** One extra ideation round aimed at the gaps the council found, then a combined re-rank.
6. **Office-hours refinement (with `--refine`).** The top idea gets a reframe, falsifiable premises, six forcing questions, and build alternatives, written to a design doc.
7. **Output.** The report is written as HTML by following a fixed build spec that reuses one reference design, so every run looks consistent.

Grounding is mixed by design: evidence is preferred and rewarded in scoring, speculative ideas are allowed but labeled and penalized 1.5 points.

## Install

```
git clone https://github.com/alexcsl/ideaforge.git ~/.claude/skills/idea-forge
```

Restart Claude Code. Works in Claude Code and Claude Cowork.

## Use

```
forge ideas: AI tools for indie game developers
ideate on B2B fintech for freelancers in Southeast Asia
generate ideas for a solo-buildable SaaS in the legal space
```

### Flags

| Flag | Values | Default | Effect |
|------|--------|---------|--------|
| `--mode` | lite, standard, deep | standard | Depth and cost. See below. |
| `--region` | ISO country code | inferred | Which communities and market reasoning to use. |
| `--lang` | ISO language code | inferred | Search and output language. |
| `--constraints` | free text | solo, 90 days, software | Budget, team, timeframe, audience. |
| `--max-searches` | integer | 3 | Per-agent search cap. |
| `--refine` | (no value) | off | Run the office-hours pass on the top idea. |
| `--dry-run` | (no value) | off | Print the run plan and cost estimate without executing. |
| `--top` | integer | 3 | How many survivors to include in the shortlist. |
| `--resume` | timestamp | off | Resume a failed run from its checkpoint (e.g. `--resume=20260602-143709`). |
| `--save` | (no value) | off | Write all outputs to disk. By default everything stays in-memory and you are asked at the end. |

Example:

```
forge ideas --region=ID --lang=id --refine --constraints="solo, under $5k, 60 days": tools for small online sellers
```

## Modes and cost

This skill spawns many sub-agents and runs many web searches, so it is heavier than a single-prompt skill. Pick the mode to match the stakes.

- **lite**: 3 ideators, no synthesizer, no peer review, capped searches. Approximately 3 agents and 6 searches. Fastest and cheapest. Good for a quick scan.
- **standard**: full swarm, synthesizer, source verification, council, peer review. Approximately 17 agents and 15 searches. The default.
- **deep**: standard plus one extra ideation round aimed at the council's gaps, then a combined re-rank, with every source verified. Approximately 28 agents and 30 searches. Most thorough and most expensive. Use it when the decision matters. A checkpoint file is written after each phase so a failed run can be resumed with `--resume`.

If your environment restricts sub-agent tools, the skill falls back to having the orchestrator run the searches and inject results into the persona agents, so it still produces grounded ideas.

## Internationalization

Idea Forge detects the language and region of your topic and adapts its sources, search language, and market reasoning. Non-English topics search the local language first, then English. The per-region source map lives in `references/sources.md` and is easy to extend.

## Files

```
idea-forge/
  SKILL.md                 the skill the agent reads
  README.md
  LICENSE
  .gitignore
  examples/
    sample-report.html     the reference report design, open it in a browser
  references/
    report-template.md     build spec for the report, points at the reference
    sources.md             per-region source map
    anonymization.md       deterministic peer-review shuffle
    refinement.md          office-hours question set
    example.md             sample input and output shape
```

## Extending it

- **Add a region**: edit `references/sources.md`.
- **Change the scoring weights**: edit the chairman weights in `SKILL.md`. Defaults: demand 0.30, problem-realness 0.25, buildability 0.20, survivability 0.15, clarity 0.10. SPECULATIVE penalty: -1.5 from the composite.
- **Restyle the report**: edit the CSS in `examples/sample-report.html`. Every run reuses that style, so changing it once changes all future reports.
- **Add or swap personas**: edit the persona lists in `SKILL.md`.

## Dynamic Workflow version

`SKILL-dynamic.md` is a separate, faster variant of the same pipeline. Triggers: `forge ideas smart:`, `dynamic ideate on`, `smart brainstorm`. Key differences from the standard version:

- Verification runs per-ideator as outputs arrive instead of waiting for all 5.
- Quality gates: auto-spawn 2 extra agents if the evidenced idea count is too low; skip the deep second round if the top idea is already very strong.
- Synthesizer is skipped when the pool is already strong.
- 3 of the 5 critics are selected dynamically based on what the idea pool most needs.
- Peer review starts after the first 3 critics return instead of waiting for all 5.
- A preliminary shortlist is shown mid-run while the remaining critics finish.
- Ideas are summarized before reaching the council, reducing token cost.
- Same output format, same security rules, same `--save` behavior as the standard version.

Use the standard version when you want the full unmodified pipeline. Use the dynamic version when wall-clock time or cost matters.

## Credit

- Council methodology: Andrej Karpathy's LLM Council.
- Sub-agent council adaptation: tenfoldmarc/llm-council-skill.
- Office-hours refinement modeled on the gstack `/office-hours` skill.
- Ideation swarm, source verification, internationalization, deep mode, and pipeline: this project.

## License

MIT. See `LICENSE`.

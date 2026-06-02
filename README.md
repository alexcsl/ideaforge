# Idea Forge

A Claude Code skill that turns a topic into a ranked, validated shortlist of ideas. Works in any language and region.

Two phases plus an optional refinement pass:

1. Ideation swarm: persona agents and a synthesizer search the web and region-appropriate communities for real problems and propose grounded ideas in parallel. Every cited source is then verified.
2. Validation council: 5 critic agents review the pool, peer-review each other anonymously, and a chairman ranks the survivors on five axes.
3. Office-hours refinement (optional): the top idea is pressure-tested with a reframe, falsifiable premises, six forcing questions, and build alternatives, then written to a design doc.

Output: a ranked shortlist in chat, a visual HTML report, and a full markdown transcript. A running kill-log stops future runs from resurfacing dead ideas.

Grounding is MIXED: real evidence is preferred and rewarded, speculative ideas are allowed but labeled and penalized. Sources are verified by fetching each URL, so fabricated citations get caught and downgraded.

## Install

```
git clone https://github.com/alexcsl/ideaforge.git ~/.claude/skills/idea-forge
```

Or copy the folder contents (with `SKILL.md` and `references/`) into `~/.claude/skills/idea-forge/`. Restart Claude Code. Works in Claude Code and Claude Cowork.

## Use

```
forge ideas: AI tools for indie game developers
ideate on B2B fintech for freelancers in Southeast Asia
generate ideas for a solo-buildable SaaS in the legal space
```

### Flags

- `--mode lite|standard|deep` (default standard)
- `--region <ISO country>` (default: inferred from topic, else global)
- `--lang <ISO language>` (default: inferred from your topic)
- `--constraints "<budget, team, timeframe, audience>"`
- `--max-searches <n>` per agent (default 3)
- `--refine` run the office-hours pass on the top idea (default off)

Example: `forge ideas --region=ID --lang=id --refine --constraints="solo, under $5k, 60 days": tools for small online sellers`

## Modes and cost

This skill spawns many sub-agents and runs many web searches, so it is heavier than a single-prompt skill.

- lite: 3 ideators, no peer review, capped searches. Fastest and cheapest. Good for a quick scan.
- standard: full swarm, synthesizer, source verification, council, peer review. The default.
- deep: standard plus a second ideation round seeded by the council's gaps, and exhaustive verification. Most thorough, most expensive.

If your environment restricts sub-agent tools, the skill falls back to having the orchestrator run searches and inject results into the persona agents, so it still works.

## Internationalization

Detects the language and region of your topic and adapts sources, search language, and market reasoning. See `references/sources.md` for the per-region source map. Non-English topics search the local language first, then English.

## Refinement

With `--refine`, the top idea runs through a YC-style office-hours pass: a reframe, falsifiable premises, six forcing questions, and 2 to 3 build approaches with effort estimates. Output is a design doc you can hand to your planning or build process. If you have the gstack `/office-hours` command installed, the skill defers to it.

## Files

```
idea-forge/
  SKILL.md
  README.md
  LICENSE
  .gitignore
  references/
    report-template.md   visual HTML report spec
    sources.md           per-region source map
    anonymization.md     deterministic peer-review shuffle
    refinement.md        office-hours question set
    example.md           sample input and output shape
```

## Credit

- Council methodology: Andrej Karpathy's LLM Council.
- Sub-agent council adaptation: tenfoldmarc/llm-council-skill.
- Office-hours refinement modeled on the gstack /office-hours skill.
- Ideation swarm, source verification, internationalization, and pipeline: this project.

## License

MIT.

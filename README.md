# Idea Forge

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-skill-7C3AED)](https://claude.ai/code)
[![Works in Cowork](https://img.shields.io/badge/Claude%20Cowork-compatible-7C3AED)](https://claude.ai)

> Turn a topic into a ranked, source-verified shortlist of startup and product ideas — grounded in real evidence, filtered by an adversarial council.

A single model asked for ideas is agreeable and ungrounded. Idea Forge runs a swarm of researcher agents that find real problems on the web, verifies every cited source, then puts the pool through a council built specifically to tell you which ideas are weak. Bad ideas get killed. Good ones get ranked.

---

## Features

- **Parallel ideation swarm** — five persona agents search the web and region-specific communities simultaneously, each from a different angle
- **Source verification** — every cited URL is fetched; fabricated or dead links are caught and the idea is downgraded to SPECULATIVE
- **Adversarial council** — five critics score on independent axes, peer-review each other anonymously, chairman ranks survivors
- **In-memory by default** — nothing writes to disk unless you add `--save`; no clutter between runs
- **Works in any language and region** — detects language and region from your topic; sources are localized automatically
- **YC-style pressure test** — `--refine` runs a reframe + falsifiable premises + six forcing questions on the top idea before you write code
- **Two versions** — standard (deterministic) and dynamic (adaptive, concurrent, lower token cost)

---

## Install

```bash
git clone https://github.com/alexcsl/ideaforge.git ~/.claude/skills/idea-forge
```

Restart Claude Code. The skill is immediately available.

> Works in Claude Code and Claude Cowork. No dependencies, no setup beyond the clone.

---

## Quick start

```
forge ideas: AI tools for indie game developers
```

```
ideate on B2B fintech for freelancers in Southeast Asia
```

```
forge ideas --mode=deep --region=ID --lang=id --refine --save: tools for small online sellers
```

---

## Triggers

| Phrase | Version |
|---|---|
| `forge ideas: <topic>` | Standard |
| `ideate on <topic>` | Standard |
| `generate ideas for <topic>` | Standard |
| `idea swarm <topic>` | Standard |
| `brainstorm and validate <topic>` | Standard |
| `forge ideas smart: <topic>` | Dynamic |
| `dynamic ideate on <topic>` | Dynamic |
| `smart brainstorm <topic>` | Dynamic |

---

## Flags

### Standard

| Flag | Values | Default | Description |
|---|---|---|---|
| `--mode` | `lite` `standard` `deep` | `standard` | Pipeline depth and cost |
| `--region` | ISO country code | inferred | Community and market context |
| `--lang` | ISO language code | inferred | Search and output language |
| `--constraints` | free text | `solo, 90 days, software` | Budget, team, timeline, audience |
| `--max-searches` | integer | `3` | Per-agent web search cap |
| `--top` | integer | `3` | Survivors in the ranked shortlist |
| `--refine` | — | off | YC-style office-hours pass on the top idea |
| `--save` | — | off | Write all outputs to disk |
| `--dry-run` | — | off | Preview run plan and cost without executing |
| `--resume` | timestamp | off | Resume a `--save` run from its checkpoint |

### Dynamic only

| Flag | Default | Description |
|---|---|---|
| `--auto-mode` | on | Picks `lite`/`standard`/`deep` from topic complexity; override with `--mode` |
| `--no-gate` | off | Disable smart-skip gates; run all phases regardless of signal strength |

---

## Modes

| Mode | Agents | Searches | When to use |
|---|---|---|---|
| `lite` | ~3 | ~6 | Quick scan, low stakes |
| `standard` | ~17 | ~15 | Default — most topics |
| `deep` | ~28 | ~30 | High-stakes decisions; adds a second ideation round aimed at council gaps |

> `--resume` requires a prior run with `--save`. Checkpoints are only written when saving is on.

---

## How it works

```
Phase 0   Framing          Parse flags, detect language and region, load kill log
Phase 1   Ideation swarm   5 persona agents search in parallel; tag each idea EVIDENCED or SPECULATIVE
Phase 1.5 Verification     Fetch every cited URL; downgrade dead or fabricated sources
Phase 2   Council          5 critics score, anonymous peer review, chairman ranks survivors
Phase 2.5 Deep round       deep mode only — extra swarm aimed at council gaps, combined re-rank
Phase 3   Output           Ranked shortlist in chat; offer to save report and transcript
Phase 4   Refinement       --refine only — reframe, premises, six forcing questions, design doc
```

**Grounding:** evidence is preferred and rewarded. SPECULATIVE ideas are allowed but labeled and penalized 1.5 composite points. No agent may invent a source.

**Composite scoring weights** (default):
`demand 0.30 · problem-realness 0.25 · buildability 0.20 · survivability 0.15 · clarity 0.10`

---

## Output

Everything stays in-memory by default. At the end you are asked whether to save.

| Artifact | In-memory (default) | With `--save` |
|---|---|---|
| Ranked shortlist | Shown in chat | Shown in chat |
| HTML report | Offered at end | `idea-forge-report-<ts>.html` |
| Full transcript | Offered at end | `idea-forge-transcript-<ts>.md` |
| Design doc (`--refine`) | Offered at end | `idea-forge-design-<ts>.md` |
| Kill log | Session only | `idea-forge-killlog.md` |
| Checkpoint | Not written | `idea-forge-checkpoint-<ts>.json` |

The HTML report style is defined in `examples/sample-report.html` — open it in a browser to preview. Every run reuses that CSS, so the output is always consistent.

---

## Standard vs Dynamic

| | Standard | Dynamic |
|---|---|---|
| **Trigger** | `forge ideas:` | `forge ideas smart:` |
| **Verification** | After all ideators finish | Concurrent per-ideator |
| **Synthesizer** | Always runs | Skipped when pool is already strong |
| **Adaptive deepening** | No | Auto-spawns 2 agents if evidenced count is low |
| **Mode selection** | User-set | Auto from topic complexity |
| **Critic selection** | Fixed 5 | 2 mandatory + 3 chosen by pool needs |
| **Peer review start** | After all 5 critics | After first 3 critics |
| **Preliminary shortlist** | No | Yes — shown mid-run |
| **Deep round skip** | Never | Yes — skipped if top idea scores above 8.5 and EVIDENCED |
| **Token cost** | Standard | Lower |

---

## Internationalization

Region and language are detected from your topic. Searches run in the local language first, then English. The source map covers:

`US/UK/CA/AU` · `India` · `Indonesia/SEA` · `Brazil/LATAM` · `Germany/DACH` · `Japan` · `China-adjacent`

Add a region by editing `references/sources.md`.

---

## Extending

- **Add a region** — edit `references/sources.md`
- **Add or swap personas** — edit the persona list in `SKILL.md`
- **Change scoring weights** — edit chairman weights in `SKILL.md`
- **Restyle the report** — edit the CSS in `examples/sample-report.html`; every future run inherits it

---

## Files

```
idea-forge/
  SKILL.md              standard pipeline
  SKILL-dynamic.md      dynamic workflow variant
  README.md
  LICENSE
  examples/
    sample-report.html  reference report design — open in a browser
  references/
    report-template.md  HTML report build spec
    sources.md          per-region community source map
    anonymization.md    deterministic peer-review shuffle
    refinement.md       office-hours question set
    example.md          sample input and output shape
```

---

## Credits

- Council methodology: [Andrej Karpathy's LLM Council](https://github.com/karpathy/llm-council)
- Sub-agent council adaptation: [tenfoldmarc/llm-council-skill](https://github.com/tenfoldmarc/llm-council-skill)
- Office-hours refinement modeled on the gstack `/office-hours` skill
- Ideation swarm, source verification, dynamic workflow, internationalization, and pipeline: this project

---

## License

[MIT](LICENSE)

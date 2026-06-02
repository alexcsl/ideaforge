# Report Template

Generate a single self-contained `idea-forge-report-<timestamp>.html` (inline CSS and JS, no external dependencies except a web font link). Dark, editorial, research-dossier feel. Avoid generic AI aesthetics: no Inter or Roboto, no purple-on-white gradients.

## Structure

1. Header: the topic and direction, run timestamp, counts (ideas generated, evidenced vs speculative, survivors).
2. The Shortlist (hero section): 3 to 5 ranked cards. Each card has a rank number, title, composite score (large), one-line rationale, biggest risk, first step, and an EVIDENCED or SPECULATIVE badge. Rank 1 is visually dominant.
3. Score breakdown: per surviving idea, a small bar row across the five critic axes (survivability, problem-realness, demand, buildability, clarity). Pure CSS bars, no chart library needed.
4. The Swarm: collapsible section listing all generated ideas grouped by persona, each with its evidence link.
5. The Council: the five critiques (anonymized labels A to E shown alongside the revealed persona), then the peer-review consensus, then the chairman verdict.
6. Refinement (only if Phase 4 ran): the reframe, accepted premises, the six answers, and the chosen approach.
7. Kill log: a table of rejected ideas and the one-line reason each died.

## Style direction

- Pick a distinctive display font (for example a serif like Fraunces or a grotesque like Space Mono for headers) paired with a clean readable body font.
- One dominant color plus one sharp accent. Subtle grain or texture on the background for depth.
- Score numbers are the visual anchors. Make them big.
- EVIDENCED badge in the accent color. SPECULATIVE badge muted or outlined.
- Staggered fade-in on load for the shortlist cards, CSS only.
- Fully responsive, readable on mobile.

## Data

The HTML is generated from the in-memory run data (framing, swarm ideas, critiques, peer reviews, chairman output, refinement, kill log). Inline the data as a JS object at the top of the file so the report is fully standalone.

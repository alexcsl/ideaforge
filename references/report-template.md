# Report Builder

This is the instruction for building the Phase 3 HTML report. There is no script. Write the HTML directly, following this spec, and match the committed reference at `examples/sample-report.html`.

## The rule that keeps output consistent

Open `examples/sample-report.html`, copy its `<style>` block verbatim, and reuse the same HTML structure. Change only the content, not the CSS. This is what makes every run look the same without a program. Do not redesign the report per run. Do not invent new fonts or colors. The reference file is the single source of truth for styling.

If the user asks for a different look, restyle the reference once and keep using it.

## Sections, in order

Render a section only if you have data for it. Partial runs still produce a valid report.

1. **Header.** The topic as the H1. A mono meta line: `mode <mode>  /  region <X>  /  lang <Y>  /  <timestamp>`. The constraints line if present. A stats row with four numbers: ideas generated, evidenced, speculative, survivors.
2. **Shortlist.** One card per surviving idea, ranked. Each card: rank number, title, EVIDENCED or SPECULATIVE badge, large composite score, one-line rationale, a row of five score bars (demand, problem-realness, buildability, survivability, clarity), then biggest risk and first step. Rank 1 uses the accent border (the `.lead` class).
3. **The Swarm** (collapsible). All generated ideas grouped by persona. Each idea shows its badge, title, one-line problem, and a source link when it has one.
4. **The Council** (collapsible, open by default). The five critiques shown as Response A to E with the persona revealed in parentheses, then the peer-review consensus, then the chairman verdict in an accent box.
5. **Office-Hours Refinement** (collapsible, open, only if Phase 4 ran). The reframe, the accepted and rejected premises, the six question answers, and the chosen approach.
6. **Kill Log** (collapsible). A two-column table: idea and the one-line reason it was killed.
7. **Footer.** One mono line noting the ranking axes.

## Data mapping

You already hold every value from the run in context: the framing, every swarm idea with its tag and source URL, the verification results, the five critiques with their revealed personas, the peer-review consensus, the chairman verdict and shortlist with composite scores and per-axis scores, the refinement answers if Phase 4 ran, and the kill log. Drop each into the matching section. Escape any user or web text so stray angle brackets do not break the page.

## Style intent (already encoded in the reference CSS)

Dark editorial dossier. A distinctive display serif for headings, a mono face for labels and metadata, a clean sans for body. One dominant warm background, one sharp accent. Score numbers are the visual anchors, kept large. EVIDENCED badge in the warm accent, SPECULATIVE badge muted and outlined. Score bars are pure CSS. Shortlist cards fade in with a staggered delay on load. Fully responsive, readable on mobile. No external dependencies except the font link. No dash glyphs anywhere, including CSS markers.

## Output

Write the file as `idea-forge-report-<timestamp>.html`, fully self-contained. Open it or give the user the path.

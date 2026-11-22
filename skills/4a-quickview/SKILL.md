---
name: 4a-quickview
description: "Assemble a dated, figure-first Markdown quickview of recent results or render requested slides from existing figures. Use when asked for a quickview, the latest figures, a Markdown figure gallery, or slides with an explicitly specified figure order and layout. Preserve the supplied content and skip scientific interpretation. Use 4b-topic-record for topic summaries, 4c-report for scientific reports, and 5-ppt-design for story-driven decks."
---

# Quick View

## Purpose

A throwaway-cheap markdown that puts recent figures in front of the user, in order,
with the minimum text needed to know what each one is.

Built for a loop where the model does the coding and plotting, and the user reads
figures and decides what matters. The document is a **viewing surface**, not a report.

**Key principle: the figure is the content. Text exists only to say what you are
looking at and where it came from.**

**Writing preferences:** if the user keeps a writing-preferences file, apply it
before writing. It overrides conflicting defaults below.

## When To Use

Trigger on:
- `quickview` / `quick view`
- "show me the latest figures"
- "put these figures in a markdown"
- "what did we just produce"
- after a batch of plots is regenerated and the user wants to look at them
- "share this quickview" / "make a pptx of this quickview" → Share step below
- an explicit slide arrangement of existing figures ("3 slides: Full / West / East, each
  raw / deseasonalized / 12m-smooth, vertical stacked") → Share step, Mode B

## Place in the workflow

```
1-plan → 3-conduct → 4a-quickview (per run) ──share──► PPTX   (fast, frequent sharing)
                          │ accumulate on one topic
                          ▼
                     4b-topic-record (per topic: 00_README.md §1 Summary / §2 Technical details)
                          │ accumulate topics
                          ▼
                     5-ppt-design (story across topics) → 6-ppt-create → PPTX → 7-ppt-to-5
```

## When NOT To Use

| Situation | Use instead |
|---|---|
| Interpreting validated results for a reader | `4c-report` |
| Slides whose story/order/grouping must be decided | `5-ppt-design` → `6-ppt-create` |
| Summarising several quickviews on one topic | `4b-topic-record` |
| Preserving reasoning or insight in prose | `noteit` |
| A durable index of where figures live | the project's own registry / `INDEX.md` |

If the user wants conclusions rather than a look at the figures, this is the wrong skill.

## Output

Filename: `quickview_<topic>_<dd-mm-yyyy>.md`

Written to the directory the user names; otherwise the closest `docs/` beside the
figures; otherwise the current directory.

Overwrite same-day files for the same topic rather than accumulating near-duplicates.

## Format

Keep the whole document scannable in one screen-scroll per figure. No frontmatter,
no section tags, no layout annotations, no speaker notes. (Exception: when the user states
a slide layout for sharing, one `<!-- layout: X -->` comment may sit in that figure block.)

**Top-to-bottom priority: scientific content first, technical/provenance detail last.**
The reader opens this to see what was produced and what it shows — not to audit how.
Everything a scientist needs to *read the result* (figure, caption, the one or two
facts that matter) goes in the main body, in order. Everything that exists to let
someone *verify* the result (producing script, exact invocation, file mtime, sha256,
data-lineage/regression checks) is provenance, not science — it is collected into one
`## Technical details` section at the very end of the document, never interleaved
between figures. A reader should be able to stop scrolling after the last figure and
already have the full scientific content; the technical appendix is opt-in reading.

```markdown
# <Topic> — quick view

<yyyy-mm-dd> · <one line: what was run / what changed since last time>

## Figure 1 — <Short figure label>

![](../plots/topic/figure.png)

**Figure 1.** <one line: what the figure shows>
- <one line: only if a second fact is needed — a number, a caveat, a definition>

---

## Figure 2 — <Next figure label>
...

---

## Technical details

| Figure | Data variables | Source script | Command / args | File mtime | Needs attention |
|---|---|---|---|---|---|
| Figure 1 | `<dataset/code variable names>` | `<script>` | `<invocation, if non-trivial>` | `<mtime>` | `<failed / not run / unverified, else blank>` |
| Figure 2 | ... | | | | |
```

Rules:

- **Relative paths** in every `![]()`, relative to the markdown file's own directory
  (e.g. `../plots/topic/fig.png`). Markdown preview plugins resolve an absolute path
  against the filesystem root and show a broken image, so absolute paths defeat the
  whole purpose of this document. Verify each one resolves *from the output
  directory* before writing.
- **Figure before text.** The image comes first under its heading, then the lines.
- **Number figures like a journal paper.** Every figure gets a single running
  `Figure N` number, assigned once in final reading order (top to bottom in the
  document), never reset per section/model/variant. The heading carries the number
  (`## Figure N — <label>`) and the first line under the image repeats it as a bold
  journal-style caption lead-in (`**Figure N.** <what it shows>`), exactly the way a
  paper captions "Figure 3. ..." beneath a panel. A group of companion figures (e.g.
  North/South, Full/East/West) is **one** Figure N with panels (a), (b), (c) side by side
  in a Markdown table (header row "(a) …", one image per cell) and one shared caption.
  Cross-reference by number in any accompanying prose
  (Diagnostic tables, comparison text) once numbers are assigned, so "see Figure 4"
  instead of re-describing the figure. If revising a quickview whose figures already
  have numbers, keep prior numbers for unchanged figures and append new ones in order
  rather than renumbering the whole set, so a number that was cited stays valid.
  The number is also the citation handle used downstream: `4b-topic-record` and the
  slide share step (`md_to_slides.py` keeps the bold `**Figure N.**` lead-in as the
  slide's first bullet) both reuse it.
- **One to two bullets per figure. Never three.** Both must be scientific content
  (what is plotted, a number, a caveat, a definition) — never a source/mtime/sha line;
  those belong only in the bottom Technical details table. If more scientific content
  is needed, the user asked for the wrong skill.
- **No bold-key prefixes, no nested bullets, no per-figure tables** unless the user asks.
- **One `Technical details` table at the end of the document**, one row per figure —
  dataset/code variable names, producing script, the exact command/args when
  non-trivial, file mtime, and a "Needs attention" cell. Do not list passed checks
  (no "PASS", sha256 or "matches to 1e-15"). Fill "Needs attention" only with a check
  that failed, was not run, or is unverified. This is reference material, not
  something the reader scans on the way to the figures — hence bottom, not per-figure.
- Captions and bullets use physical quantities, not dataset/code variable names; those
  go in the Technical details table.
- `---` between figures, and one more before the Technical details section.
- Companion figures (North / South, Full / East / West, or precipitation / SST / wind) form one figure
  with side-by-side panels and a single shared bullet set (one row-group in the bottom
  table).

## Content Rules

- State **only what is documented** — from the producing script, its text output, a
  validation record, or the user's own words in this session.
- **No interpretation.** Do not say a result is consistent, promising, surprising, or
  physically meaningful. Say what is plotted.
- Numbers are welcome when they come from a record or printed output; never estimate
  one by reading it off a figure.
- If something about a figure is unknown, write `unknown` rather than inferring it.
- If a figure is older than the script that makes it, mark the heading `[stale]`.

## Workflow

1. **Resolve the figure set.** Use exactly the figures the user named. If they said
   "the latest", list candidates with mtimes and confirm the set before writing —
   do not guess which ones they mean.
2. **Verify every path resolves from the output directory.** `cd` to where the
   markdown will live and test each relative link; never write one you have not checked.
3. **Find each producing script** where it is cheap to do so (grep the output
   filename). Record it as a row in the bottom Technical details table, not inline
   with the figure. Write `unknown` if not found quickly — do not go hunting.
4. **Check staleness** — compare figure mtime against its producing script; mark a
   stale figure's heading `[stale]` (main body) and still record the mtime comparison
   in the Technical details row.
5. **Write the file**, then open it in the VS Code editor (`code <path>`; use the
   full path to the file just written) so the user lands on it directly, then report
   its path and the figure count. If the `code` CLI is unavailable, skip the open and
   say so — this is a convenience, not a blocking step.

Do not ask about layout, colors, or ordering. Default to the order the user listed the
figures, or chronological if they did not.

## Safety

- Never modify, move, or regenerate a figure. This skill only reads and links.
- Never write into a directory holding paper or manuscript assets.
- If a figure is a known paper-locked source, note that on its `Source:` line and
  otherwise leave it alone.

## Share step — PPTX (optional, only when asked)

The quickview stays the default deliverable. Render a PPTX only when the user asks to
share, present, or "make slides". This step makes **no content decisions**: no cover
text, bullets, notes, reordering or interpretation beyond what the markdown already holds.

Set `SKILL_DIR` to the installed skill folder. Use Python with Pillow and python-pptx. Set `PY` to that interpreter.

### Mode A — from a quickview (or any figure-first markdown)

```bash
$PY $SKILL_DIR/../6-ppt-create/md_to_slides.py quickview_<topic>_<date>.md --render [--tag-prefix REGION] [--layout vstack-3] [--no-cover]
```

- One `## ` block → one slide, in order. `# Title` + date line → cover slide.
- `**Figure N.**` caption → first bullet of its slide, **keeping the bold `Figure N.` lead-in** so a
  slide can be cited like a paper figure; `- bullets` follow (≤ 3 in all; extras → speaker notes);
  other paragraphs → speaker notes. `## Technical details` → cover speaker notes.
- Layout: a `<!-- layout: X -->` comment inside a block wins, then `--layout`, then auto
  (1 fig → `figure-dominant` with bullets / `single` without; 4 → `grid-2x2`).
- **Several figures under one heading:** the direct python-pptx engine keeps text and up to four
  figures on one slide; the layout follows the figure count and aspect (pair, `hstack-3`/`vstack-3`,
  `grid-2x2`). A figure count with no supported layout stops with a message: split the block or set
  `<!-- layout: X -->`. Nothing is written in that case.
- Inline TeX is mapped to slide text by `6-ppt-create/latex_text.py`; the markdown keeps TeX.
- Output: `<source>_slides_<dd-mm-yyyy>.md` (6-ppt-create storyboard) + `.index.json` + `.pptx`
  + `_postprocess.py` beside the source (use `-o` for another location, e.g. `docs/slides/`).
- The same command renders a topic record's Section 1: `6-ppt-create/md_to_slides.py 00_README.md --section Summary --render` (`# 1 Summary` and `# L2 · Summary` both match).
- `md_to_slides.py` lives in `6-ppt-create` as its Markdown input adapter; a forwarding shim stays at the old path in this folder.

### Mode B — explicit arrangement of files in a directory

**Authority rule:** the user's explicit slide count, grouping, order and layout beat every
heuristic. `vertical stacked` → `vstack-N` even if the aspect rule disagrees (note the mismatch in
one line; do not override). Never add cover, outline, summary, bullets or notes unless asked.

1. Write `spec.json` (scratchpad): `dir`, `topic`, `title` (tag prefix), `layout`,
   `figure_height` (default `13/14`), `groups` (one slide each: `name`, `tokens` aliases),
   `roles` (figures per slide: prefer one real `example` filename; fallback `include`/`exclude`
   tokens; `shared: true` for a figure on every slide), optional `overrides {"Group|Role": file}`
   **only** after the user picks from an ambiguity list.
2. Dry run: `$PY $SKILL_DIR/assemble_figures.py spec.json` → prints the matrix.
   `[MISSING]` → report, never substitute. `[AMBIGUOUS]` → show candidates, ask, add `overrides`.
3. `$PY $SKILL_DIR/assemble_figures.py spec.json --write -o <out>/<topic>_<dd-mm-yyyy>.md`
4. `$PY $SKILL_DIR/../6-ppt-create/md_to_slides.py <out>/<topic>_<dd-mm-yyyy>.md --render-only`

### What `--render` / `--render-only` does (6-ppt-create phases, chained)

`6-ppt-create/render_storyboard.py` (default `pptx` engine: `build_pptx.py` → `postprocess_template.py`
with IDs stamped, so `7-ppt-to-5` can round-trip a hand-edited deck → `<name>.layout.json` recorded).
Rendering logic lives only in 6-ppt-create; do not duplicate it here.
For layouts outside the quick set (`grid-2x3`, `focus-context`, table slides) use 6-ppt-create directly.

Report: storyboard path, pptx path, slide list (index · layout · figure count · tag).

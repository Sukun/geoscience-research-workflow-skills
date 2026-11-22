---
name: 5-ppt-design
description: "Create a topic-and-date-named slide storyboard Markdown file from a rough user draft or a research notes subset. Use when deciding the story, slide order, selected figures, and layout before rendering. Do not export PPTX; use 6-ppt-create for mechanical rendering."
---
# ppt_design — Slide Storyboard Skill

## Presentation preferences

If the user keeps a presentation or writing-preferences file, apply it (see **Writing preferences** below); explicit instructions for the current task take priority.

Outlines and storyboards are for sharing with colleagues and reviewers: write all slide text and `::: notes` to the [sharing copy rules](../6-ppt-create/references/sharing-copy.md) (no paths, links or storage details, no status or to-do labels, plain natural language, one-line take-home titles that follow from the previous slide, every number kept, no facts added). Figure paths stay only in `![]()` and annotation comments, which the renderer consumes and never shows. Where this file says to put file paths or TODO notes in `::: notes`, use an HTML comment instead.

## Create vs revise

This skill is the **create** step: `/5-ppt-design` → `<name>.md` → `/6-ppt-create` → `<name>.pptx` + `<name>.layout.json`. Hand edits made in PowerPoint go through the **revise** loop (`/7-ppt-to-5` → `/6-ppt-create`), which updates `<name>.md` and `<name>.layout.json` in place without this skill.

Come back here only to change the story (reorder sections, merge, split, add or cut slides) of a deck that already has a layout file. Then:
- Edit the existing `<name>.md`; do not write a new file name, or the layout file is not found.
- Give every slide a `<!-- slide-id: … -->` and never renumber or reuse existing IDs: the layout file matches by ID. A new slide gets a new ID; a merged slide keeps the ID of the slide it absorbs into; a split slide keeps the ID on one part.
- If the PPTX was edited since the last render, run `/7-ppt-to-5` first. Where a slide was edited in both places, the newer file wins.

## Purpose

Produce a slide-by-slide storyboard markdown file from either:

- **Mode A**: a rough user draft (free-form bullets, spoken description, or section titles)
- **Mode B**: a pointer to one or more research notes files (reads and proposes a storyboard)
  - **Default Mode-B source: topic records.** When `<topic>/00_README.md` files exist (made by
    `4b-topic-record`), read only their `# 1 Summary` sections. They are already slide-shaped
    (one `## 1.k` per slide, a key figure, ≤ 3 cited bullets, finding-style titles). This skill's job is then only
    the **cross-topic story**: which topics, their order, which Section 1 subsections to cut or merge, the cover,
    outline and summary slides, and section tags. Pull a Section 2 number or companion figure only when
    a slide needs it, and cite the §2.k subsection in speaker notes. Read full notes only if no topic record exists.
  - A single topic needs no storyboard design: `6-ppt-create/md_to_slides.py 00_README.md --section Summary --render`.

Both modes produce a Markdown storyboard with explicit layout annotations that `6-ppt-create` reads to produce the final PPTX.

This skill makes **content decisions**. It does NOT touch Pandoc or PPTX formatting — those belong to 6-ppt-create.

**Writing preferences:** if the user keeps a writing-preferences file, apply it before writing the storyboard. It overrides conflicting defaults below. Slide bullets use physical terms. Variable names, caveats and LaTeX formulas go to speaker notes, and passed checks are omitted.


---

## Parameters

| # | Parameter      | Default                                                                        | Description                                                  |
| - | -------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------ |
| 1 | `SOURCE`     | User message or current directory, always use absolute paths for files/figures | Free-form draft text OR path to notes file(s)                |
| 2 | `TOPIC`      | Inferred from source                                                           | Short topic slug for output filename (e.g.`rainfall_progress`) |
| 3 | `NOTES_DIR`  | Current working directory                                                      | Directory to scan for figure files                           |
| 4 | `MAX_SLIDES` | 25                                                                             | Soft cap on content slides (excluding title + appendix)      |

If parameters are omitted, infer from context. Do not ask — proceed to Phase 1.

---

## Output File

Filename: `<topic>_<dd-mm-yyyy>.md` (e.g. `rainfall_progress_22-05-2026.md`)

Written to the user's current working directory (or `NOTES_DIR` if set).

---

## Storyboard Format

The storyboard is near-Pandoc slide format. `6-ppt-create` converts layout annotations to final Pandoc syntax in-place.

### Frontmatter

Use bare double-dashes only — **no `title:` field** (a YAML `title:` creates a duplicate cover slide):

```yaml
---
---
```

### Cover slide (Slide 1)

The cover uses `## Heading` for the title. This heading is kept visible.

```markdown
---

<!-- Slide 1 -->
## Presentation Title

- Sub-bullet or one-sentence overview

::: notes
[Context for the presenter]
:::
```

### Outline / Roadmap slide (first content slide after cover)

The first content slide is always an **outline slide**, not a data-summary slide.
It gives the audience a roadmap of the talk using the section tag labels as bullet points.

```markdown
---

<!-- Slide 2 -->
<!-- section-tag: "Outline" color="steelblue" -->
## Outline

- **Monsoon region** — seasonal rainfall totals and multi-model spread
- **Arid subtropics** — dry-season trends and their significance
- **Station network** — 40-year gauge record and model skill
- **Summary** — cross-section comparison and key findings

::: notes
[Roadmap for the audience. The section tags on subsequent slides match these labels.]
:::
```

Rules for the outline slide:

- Use `<!-- layout: text-only -->` when no map figure is available.
- Use `<!-- layout: figure-dominant -->` when a study-area map exists — place the map as the figure.
- Bullet labels must **exactly match** the `<!-- section-tag: "..." -->` labels used on subsequent slides.
- Max 5 bullets (one per major section + any appendix note).
- Keep bullet text ≤ one line: **section name** (bold) + em-dash + one-phrase description.

### Content slides (Slides 2+)

**Every slide — including content slides — must have a `## Heading`** so Pandoc places all content
(bullets + images) on a single slide. The heading text is the same as the section tag label.
`6-ppt-create` Phase 4 clears the heading text from the title placeholder; the section tag chip at
top-left is the visible slide identifier.

```markdown
---

<!-- layout: figure-dominant -->
<!-- section-tag: "Section Name" color="colorname" -->
## Section Name

- **Key conclusion** — one line
- **Second conclusion** — one line

![](/absolute/path/to/fig.png)

::: notes
[Speaker notes — methodology, exact statistics, caveats. NOT shown on slide.]
:::
```

### Figure path rule

**Always use absolute paths** for all figure references — in `![]()`, in `<!-- figures: ... -->`, and in `<!-- TODO: ... -->` placeholders.

- ✓ `![](/path/to/project/figures/result.png)`
- ❌ `![](../plots/stations/trend/fig.png)`

Relative paths break when the storyboard is moved or when 6-ppt-create resolves figures from a different working directory. Absolute paths are unambiguous regardless of where the PPTX is generated.

### Section tag spec

- Format: `<!-- section-tag: "Label Text" color="colorname" -->`
- Placement: rendered by 6-ppt-create at top-left, **24 pt bold white** on coloured background
- Available colors: `steelblue`, `darkorange`, `teal`, `purple`, `crimson`
- Use the same section tag for consecutive slides in the same thematic group
- Keep in the `.md` file only; do NOT copy into `::: notes` blocks

### Layout tags (required — select based on figure count and aspect ratio; breakeven A = 2.3 for vstack vs hstack)

Choose the tag and write column syntax for equal widths only. `6-ppt-create` detects existing column syntax and preserves it.

| Tag                                  | Meaning                                       | When to use                                                               |
| ------------------------------------ | --------------------------------------------- | ------------------------------------------------------------------------- |
| `<!-- layout: single -->`          | 1 figure, full slide                          | One result — no text bullets needed; use whole slide area                 |
| `<!-- layout: figure-dominant -->` | Bullets top + figure bottom                   | Standard single-figure slide (1–3 bullets)                                |
| `<!-- layout: vstack-2 -->`        | 2 wide figures stacked vertically             | 2 figures with aspect **≥ 2.3**                                          |
| `<!-- layout: vstack-3 -->`        | 3 wide figures stacked                        | 3 figures with aspect **≥ 2.3** only — see aspect threshold rule below   |
| `<!-- layout: hstack-2 -->`        | 2 portrait figures side-by-side               | 2 figures with aspect **< 2.3**                                          |
| `<!-- layout: hstack-3 -->`        | 3 portrait figures on ONE slide (python-pptx) | 3 figures with aspect **< 2.3** — MUST add `<!-- figures: ... -->` line  |
| `<!-- layout: grid-2x2 -->`        | 4 figures in 2×2 grid                        | Multi-model comparison panels                                             |
| `<!-- layout: grid-2x3 -->`        | 6 figures in 2×3 grid                        | 6-ppt-create generates a dedicated python-pptx script                     |
| `<!-- layout: focus-context -->`   | 1 main (65%) + 2 thumbnails (35%)             | Primary result + supporting panels                                        |
| `<!-- layout: text-only -->`       | No figures                                    | Title, summary, or transition slides                                      |

**When layout cannot be determined from the rules above** — if figure aspect ratio is unknown or a slide's content makes the best layout genuinely ambiguous — do not guess. Ask the user before writing Phase 2:
> "Slide N: figure at `[path]` — aspect ratio unknown or layout unclear. Please confirm: vstack-3 / hstack-3 / figure-dominant?"
Proceed to Phase 2 only after the user confirms.

### vstack vs hstack aspect threshold — CRITICAL RULE

**Always choose between vstack and hstack based on figure aspect ratio, not semantics.**

A 16:9 slide is ~13" wide × 7.5" tall (available figure area ≈ 13" × 5.7" after text band + margins). The image area produced by each layout for N=3 figures is:

| Layout | Image area (sq-in) |
|---|---|
| **hstack-3** | ≈ 18.75 / A |
| **vstack-3** | ≈ 3.61 × A |

Breakeven: **A ≈ 2.3**

- **A ≥ 2.3 → vstack-3**: wide landscape figures (timeseries, 12m-smoothed anomalies). Rows exploit the slide width.
- **A < 2.3 → hstack-3**: portrait, square, or mildly landscape figures (sections, Hovmöllers, profiles). Columns exploit the wider dimension. At A=1 (square), hstack-3 gives **5× more image area** than vstack-3.

Never choose vstack-3 because "stacking feels intuitive for Full/East/West comparison" — use the aspect ratio to decide.

The same threshold applies to 2-figure layouts: vstack-2 (A ≥ 2.3) vs hstack-2 (A < 2.3).

---

## Content Rules

### Slide titles

- **Cover slide only** shows its heading visibly. All other slides use `## Section Tag Text`
  (same text as the section tag label); 6-ppt-create clears it from the title placeholder.
- Keep cover title ≤ 65 characters.
- **No number prefixes.** Slide ordering is tracked via `<!-- Slide N -->` comments only.

### Bullet text — the "one line" rule

- **Target one line per bullet.** If a formula or very long technical term cannot fit one line, allow two — this is the only exception.
- Bullets state **what the audience must conclude**, not what is visually self-evident.
- **Good bullets point to the non-obvious insight:**
  - ✓ "**ENSO phase** explains >60% of interannual rainfall variance"
  - ✓ "**Post-satellite spread** halves → observations constrain the reanalyses"
- Max 3 bullet points on `figure-dominant`. Zero on `single`.
- For `vstack-2`: max 2 bullets below figures (space is limited).

### Bullet structure for figure slides (≤4 figures per slide)

**Guideline**: Use **1–3 bullets** before citing figures to frame the key message and highlight findings.

**Bullet 1–2: Frame & details** (concise; one line maximum unless exceeding line exceeds one line, then two lines allowed, if necessary, for formulas or technical terms, if too long, rephrase to be more concise)
- Further introduce **what the figure shows**: e.g. left panel shows *precipitation*, right panel shows *SST*
- Describe **methodology**: e.g. "variables computed by time-mean difference" or "anomalies relative to a per-calendar-month climatology"
- Highlight **content** of each panel or variable

**Bullet 3: Findings & phenomena** (optional; one line)
- Highlight **obvious phenomena or key findings** from the figures
- Use **NO numbers** — quantitative results go to `::: notes`
- Point to visual patterns: e.g. "wet bias over orography" or "trend strongest in the dry season"

**Section tag structure** (last level only):
- Last heading level (`###...`) must mark the **figure key variables/type concisely**, e.g.  
  `### big concept | Precipitation & SST | Seasonal means`  or  `### Rainfall Timeseries | Model vs Gauges`
- **Strict rule**: total section-tag text must be **≤ 65 characters** (including brackets, colons, commas)
- from high-level to low-level: general to specific, e.g. `Rainfall` → `Rainfall Hovmöllers` → `Rainfall Hovmöllers | Full Domain`


**Examples**:
```markdown
### Precipitation & SST | Zonal-mean view

- **Left panel** shows precipitation (mm day⁻¹, color) and SST (°C, black contours) across latitude.
- **Right panel** is the precipitation anomaly normalized by its climatological standard deviation.

![](/path/to/zonal_hovmoller.png)

::: notes
Anomalies computed relative to the 1991–2020 per-calendar-month climatology.
SST contour interval = 2 °C.
:::
```

```markdown
### Rainfall | Multi-Model Timeseries

- Multi-model ensemble members in **blue**; ensemble mean in **red** (3-year smoothing).
- Gauge-based timeseries overlaid in **black** for direct comparison.
- Large ensemble spread after 2008 reflects decadal variability.

![](/path/to/timeseries.png)

::: notes
Ensemble mean rainfall = 3.2 ± 0.4 mm day⁻¹.
Gauge mean = 3.0 mm day⁻¹ over same period.
:::
```

### Companion figures — always share one slide

**Never split companion figures across multiple slides.** Companion figures are a set that share the same variable type, time period, or method across sub-regions (e.g. precipitation, SST and wind Hovmöllers for the same domain), or the same diagnostic across sub-arrays (Full, East, West).

- Precipitation + SST + wind Hovmöllers → one `hstack-3` slide, NOT separate `single` + `hstack-2`
- Full + East + West timeseries → one `vstack-3` or `hstack-3` slide (per aspect threshold)
- Two complementary diagnostics (e.g. rainfall vs soil moisture) → one `hstack-2` slide

If the figure aspect makes a combined layout too cramped, prefer `hstack-3` (python-pptx rebuild handles sizing automatically) over splitting into separate slides. Splitting companion figures wastes slides and breaks visual comparison.

### Column syntax — preview-friendly format

Write storyboards **with Pandoc column syntax** so figures are visible as inline previews in VSCode Markdown Preview. Use **equal-width columns only** — never `{.column width="70%"}` or other asymmetric widths. Omit the width attribute entirely: Pandoc defaults to 50/50.

**`figure-dominant`** (bullets left, figure right):
```markdown
<!-- layout: figure-dominant -->
<!-- section-tag: "Section" color="steelblue" -->
## Section

:::: {.columns}
::: {.column}
- **Key finding 1**
- **Key finding 2**

:::
::: {.column}
![](/absolute/path/to/fig.png){width="100%"}

:::
::::

::: notes
[Speaker notes only]
:::
```

**`hstack-2`** (two figures side by side):
```markdown
<!-- layout: hstack-2 -->
<!-- section-tag: "Section" color="darkorange" -->
## Section

:::: {.columns}
::: {.column}
![](/absolute/path/to/fig_a.png){width="100%"}

:::
::: {.column}
![](/absolute/path/to/fig_b.png){width="100%"}

:::
::::

::: notes
[Speaker notes only]
:::
```

**Exception — `hstack-3`**: Pandoc cannot render 3-column natively. Write only the first figure as `![]()` and add a `<!-- figures: a, b, c -->` comment; 6-ppt-create's Phase 4D handles it via python-pptx. Do NOT write column syntax for hstack-3.

6-ppt-create Phase 2 detects pre-formatted storyboards (column syntax already present) and skips re-adding it.

### Blank line before every closing `:::`/`::::` fence — CRITICAL RULE

**Always leave one blank line between the last content line (bullet or image) inside a fenced div and its closing fence.** Without it, a bare `:::` immediately below a `- bullet` line reads as a list-continuation line to markdown auto-formatters (VSCode's built-in formatter, prettier, format-on-save). They indent it to align with the list item — and an indented `:::` is no longer at column 1, so Pandoc silently fails to close the div. The visible symptom is a near-empty `.pptx` (tens of KB instead of the expected MB-scale file with images), with `[WARNING] Div ... unclosed` from Pandoc on export. This has broken a real storyboard after an editor reformat — treat the blank line as mandatory, not cosmetic, on every `:::`/`::::` closer that follows a bullet list.

### Note: blocks

- **Only write content explicitly stated in the source notes or documentation.** only write what you actually know from the source. Do not infer, embellish, or reconstruct from general domain knowledge.

- `::: notes` text goes to the **PowerPoint speaker Notes pane** — NOT shown on slide.
- Put here: exact numbers (r, RMSE, bias), methodology details, caveats, references.
- **Do NOT** include: section tag color/placement info, aspect ratios, pixel dimensions, or 6-ppt-create mechanism descriptions (e.g. ❌ "HSTACK-3 SLIDE — Phase 4D handles this").
- Do **not** repeat bullet content in `::: notes`.
- **Format**: one sentence per line. No run-on paragraphs.
  - ✓ `Multi-model mean = 3.2 mm day⁻¹.`  `Spread = 0.4 mm day⁻¹ (1σ).`  `Gauges = 3.0 mm day⁻¹.`
  - ❌ `The multi-model mean rainfall is 3.2 mm day⁻¹ with a spread of 0.4 mm day⁻¹ and the gauges give 3.0 mm day⁻¹.`

- **Algorithm:** — For slides whose figure shows a derived quantity (transport, stream
  function, anomaly, METRIC component, skill score), add an **Algorithm:** subsection at
  the end of the `::: notes` block. 
  - If the algorithm is not documented, write: `**Algorithm:**`
  - **Never infer, reconstruct, or embellish** from general domain knowledge.
  - Example (only when the source states this): `**Algorithm:** Seasonal anomaly: subtract the per-calendar-month climatology, then average over the season; trend from ordinary least squares. analysis.py → seasonal_anomaly().`

  Omit the Algorithm section for slides that show only raw observational time series
  (no model computation involved), or for slides that show skill scores without documenting the underlying algorithm.

### Coordinate-space grouping rule

- When results exist in two vertical or coordinate systems (e.g. height and pressure levels, depth and density), **group them**: all slides in the first system, then all slides in the second.
- Never interleave the two coordinate systems within the same section.

### General content filtering

- **KEEP on slides**: key findings, the single most important statistic per panel
- **MOVE TO notes**: exact numbers, caveats, method details, formulas, grid indices
- **OMIT entirely**: code names, script paths, variable names, internal file references

---

## Execution Workflow

### PHASE 1 — Explore & Outline (Plan Mode preferred, read-only)

**If Mode A (user draft provided):**

1. Parse the user's draft to extract intended slide order, section groupings, and figure references.
2. Scan `NOTES_DIR` for available figure files.
3. Identify gaps (figures mentioned but not found).

**If Mode B (notes file provided):**

0. If the sources are topic records, extract their `# 1 Summary` sections first and apply the line-count check to those sections, not the whole files.
1. Check line count: `wc -l <notes_file>`. If > 150 lines, ask the user: "Notes file is ~N lines — read in full to construct the storyboard? (yes / no — paste a summary instead)". Proceed only on yes.
2. Read the specified notes file(s).
3. Identify key findings, figure references, and thematic groupings.
4. Ask: "Full narrative arc + slide table (thorough) or slide-by-slide table only (fast)? (arc / table)" — construct accordingly.
5. Apply coordinate-space grouping.

**Present proposed outline:**

```
# STORYBOARD — Proposed Outline

## Narrative arc
[The story in plain language]

## Proposed slide sequence
| # | Section tag | Layout | Figures |
|---|-------------|--------|---------|
| 1 | (cover) | text-only | — |
| 2 | seasonal means | figure-dominant | timeseries.png |
...

## Figures found vs. missing
- ✓ fig1_rainfall_timeseries.png
- ✗ station_overview_map.png — [TODO]
```

**Risk-awareness check (required before presenting outline):**
- For each figure path in the proposed outline, verify it exists: `ls <path>`. Mark missing paths `✗ [missing]` in the figures table.
- If ≥ 2 figures are missing, warn: "N figure paths could not be verified — storyboard will include `<!-- TODO: confirm path -->` placeholders. Proceed anyway?"
- If a figure exists but its aspect ratio cannot be determined (binary file unreadable), flag with `[aspect unknown]` and ask the user before choosing a layout.
- If `MAX_SLIDES` would be exceeded, warn and propose a merged or abbreviated deck.

**Proceed decision**: if the proposed deck is ≤ 3 slides, proceed to Phase 2 immediately. Otherwise present the outline and ask: "Proceed to write the storyboard? (yes / adjust)"

### PHASE 2 — Write Storyboard (only after approval)

1. Write `<topic>_<dd-mm-yyyy>.md` in `NOTES_DIR`.
2. Apply all format rules and layout tags above.
3. Cover slide: use `## Heading` (kept visible).
4. Slide 2 (first content slide after cover): generate the outline/roadmap slide — section tag labels
   as bullets, use `figure-dominant` when a study-area map is available, otherwise `text-only`.
5. All other slides: use `---` + `<!-- section-tag: ... -->` + `## Section Tag Text` (same text).
6. Mark uncertain figure paths: `![](PLACEHOLDER_figure_name.png)` with `<!-- TODO: confirm path -->`. This is the only permitted exception to the absolute path rule — the placeholder name makes the intent explicit.
7. Report the output file path and prompt: "Storyboard ready. To export to PPTX, run `/6-ppt-create <path>`."

### PHASE 3 — Iterate (on user request)

When the user edits the storyboard and asks for refinement of specific slides:

1. Read the current storyboard file.
2. Apply requested changes to the specified slides only.
3. Do not touch slides the user did not mention.
4. Report what changed.

---

## Safety Constraints

- Phase 1 is strictly read-only — no files written.
- Phase 2 activates only after explicit user approval.
- Never modify or delete source notes files.
- Never fabricate results or figures — mark unknowns with `[TODO]`.
- Never use paper-style section headings (Abstract, Introduction, etc.) as slide titles.
- 6-ppt-create handles all PPTX formatting — do not add Pandoc column syntax here.

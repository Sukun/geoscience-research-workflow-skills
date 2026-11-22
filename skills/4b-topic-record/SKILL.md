---
name: 4b-topic-record
description: "Consolidate existing notes and results on one research topic into a practical 00_README.md with key figures beside relevant Summary points, technical details and final formula/code/data records. Use for topic records, topic-folder consolidation or resuming a topic. Project mode refreshes the project README.md (L1). No new analysis or interpretation; not for manuscripts or single-run quickviews."
---

# Topic Record

## Purpose

One practical research record per topic. **Section 1 (`# L2 · Summary`)** brings the question,
results, key figures and caveats together. Put each key figure beside the summary points it
supports. **Section 2 (L3, collapsed `<details>` block)** holds remaining figures, methods and
supporting detail. Supporting topic notes carry the level in the filename (`L3_NN_…`), and
agent records live in the topic's `records/<YYYY-MM-DD_slug>/`.
This is a working research note, not a journal paper.

```
4a-quickview (per run) ──accumulate──► 4b-topic-record (per topic) ──several topics──► 5-ppt-design → 6-ppt-create
```

This skill **condenses and organises existing documented facts**. It runs no analysis, makes no
new figures and adds no new interpretation.

**Writing preferences:** if the user keeps a writing-preferences file, apply it before
writing; it overrides conflicting defaults below. Section 1 uses physical terms only. Method detail
goes to Section 2. Variable names, file paths and scripts go to the final code/data section. Passed checks are not
recorded.

## When To Use

- "topic record", "summarise / organise / consolidate this topic (folder)"
- several quickviews or reports have piled up in one topic folder
- the user wants to pick up a topic after weeks away
- refreshing an existing `00_README.md` after new notes land in the folder

| Situation | Use instead |
|---|---|
| Maintain the project's `RESEARCH_NOTES.md` index and `notes/` | `notes-organizer` |
| Look at one run's figures | `4a-quickview` |
| Interpretation / write-up for readers | `4c-report` |
| A deck across several topics | `5-ppt-design` (reads each record's Section 1) |
| Log one finding or decision | `log-finding` / `noteit` |

## Output

- `<topic_dir>/00_README.md` — the record (overwrite on refresh; keep existing log history).
- The topic's retained notes renamed `L3_01_…`, `L3_02_…` (or `L2_NN_…` for a summary-level
  companion) in **oldest first** order; plans, contracts and run records move to
  the topic's `records/<YYYY-MM-DD_slug>/` instead.
- Updated links in the parent index files that point at renamed notes.

## Structure of `00_README.md`

Reading order: summary points and their figures together, then technical detail. Only formulas
and code/data records belong at the bottom. Preserve useful existing L1/L2 headings; do not
force a paper-style structure or renumber headings solely for slide compatibility.

```markdown
# <Topic> — topic record

<yyyy-mm-dd>: <one line: where the topic stands now>

---

# 1 Summary

## 1.1 Question and framework        ← 2–3 bullets: question, key definitions, conventions

- <statement> (§2.1).

## 1.2 <Result stated as a finding>   ← ≤ 3 bullets with numbers; end with → **Continue/Stop** if the source says so

- <statement with number> (§2.2; Figure 1).
- <statement> (§2.2; Eq. A1).

![](output/key_figure.png)

**Figure 1.** <what is plotted: data, window, smoothing>

… (≤ 6 result subsections)

## 1.N Open decisions                 ← ONLY if the sources record open questions; otherwise omit the subsection entirely

---

# 2 Technical details

## 2.1 <same title stem as 1.1>
Compact paragraph(s) or bullets: data, window, method, all numbers, flags, caveats. Cite figures
as "Figure N" and equations as "Eq. (A1)". Link source notes inline where they are relevant,
with a phrase saying what the reader finds there, e.g. "The method note
[01_method.md](docs/01_method.md) explains how the routine is applied row by row."

## 2.2 <same title stem as 1.2>
…

---

# 3 Formulas

$$
… \tag{A1}
$$

---

# 4 Code and data records

| Step | Script | Output |
|---|---|---|

- Datasets: path, variables, dimensions, units. Environment. Downstream users of each output.

---

# 5 Log (newest first)     ← optional; omit for a topic with a single stage
- yyyy-mm-dd — what was done (→ §1.x)
```

Traceability and numbering:

- **Subsection IDs are fixed and citable.** Section 1 uses `1.1, 1.2, …`; Section 2 uses
  `2.1, 2.2, …`, and `2.k` holds the detail for `1.k` (same title stem). Section 2 may add extra
  subsections after the mirrored ones.
- Use concise references to supporting detail, figures or equations where useful. Keep quantitative
  results traceable to existing evidence; do not force citations onto every framing bullet.
- No numbered "Source notes" table. Link each note inline in Section 2 (or the final code/data section for pure
  data records), with a phrase saying what it contains.
- Embed key figures in their relevant Section 1 subsections. Supporting figures may sit beside
  their discussion in Section 2. Do not duplicate images in a separate bottom gallery.
- Keep display equations, code, file paths and dataset variable names out of Section 1.
  Inline physical symbols and numbers are fine.

Figure numbering:

- **One running `Figure N` per record**, assigned in order of **first citation** in the text.
  Embed each figure beside its relevant summary or technical discussion. Never restart per section.
  Companion panels (Full/East/West, T/S/σ) form one figure with labelled panels per the writing
  preferences, unless the user asks otherwise.
- **Every embedded figure is followed directly by `**Figure N.** <caption>`**, one line saying what
  is plotted (data, period, smoothing), with no interpretation.
- Text that points at a figure says "Figure N" rather than re-describing it.
- When refreshing an existing record, keep the numbers of unchanged figures and append new ones
  at the end (the next unused N). Don't renumber, because cited numbers must stay valid.
  A removed figure leaves its number unused.
- Figures that stay links only (a list of further dates or panels) are not numbered.
- If a note already carries its own `Figure N`, the record's number wins. Give the source's
  number in the caption as "(source note 04, its Fig 2)" so both stay traceable.

Equation numbering: `(A1), (A2), …` in the final Formulas section, written with `\tag{A1}` inside `$$ … $$`, with
the `$$` delimiters on their own lines. Each equation is cited at least once from Section 1 or 2.

Rules for Section 1:

- Every subsection is a `## 1.k` heading. **Titles state the finding**, not the topic
  ("Cross-latitude coupling is weak", not "Cross-latitude results"). Keep titles ≤ 65 characters
  after TeX → Unicode.
- Aim for ≤ 3 concise bullets per subsection, followed by the relevant figure and caption.
  Put extended method detail in Section 2.
- No level-1 headings inside Section 1, no tables, no nested bullets.
- 1.1 is the framework. Results follow in reading order. Open decisions, if any, come last.
- Keep key images inside the Summary so summary-based slide extraction can include them.
  For existing L1/L2 records, select the corresponding summary heading when exporting.

## Content rules

- **Only documented facts** from the topic's notes, run records, validation files, or the user's
  words. Numbers must come from those sources and never be read off a figure.
- Keep the source's labels: an interpretation stays tagged **[INT]** (or "the report's reading"),
  and an observation stays plain. Never upgrade a hedge.
- Where sources conflict, keep both, with dates, and say which supersedes which. Don't choose silently.
- Mark stale content (figure older than a later decision, superseded definition) as **[stale]** with
  the reason, unless the user asks for stale content to be removed.
- Relative figure paths only, verified from the topic directory. Never copy figures into `docs/`.

## Workflow

For layout-only revisions, edit only the requested record and relevant skill rules. Skip
renames, full-topic reinventory and parent-index updates unless separately requested.

1. **Inventory (read-only).** List the topic folder's files (size, mtime, symlink targets) and grep
   the repo for references to each filename, including code comments, other docs and
   `records/` folders. Read every note in full.
   **Staleness check:** list decisions, definition changes and data reprocessing dated after each
   figure or number (project README definitions, `records/`, git log). Tag affected items **[stale]** with the
   reason; never fix them here.
2. **Propose the rename table** (old → new, oldest first; plans/records → the topic's `records/`) and show which files
   reference each old name. Ask for approval before touching anything, unless the user already
   approved the renames for this folder.
   - Plans kept elsewhere (for example symlinks into `plans/`) keep their name and are linked first
     in Section 2.
3. **Rename** with `mv` (not `git mv`, so nothing is staged). Never leave alias symlinks;
   name in your report any old name still cited by a file you may not edit (paper repo,
   paper-locked sources). Update live references (READMEs, code comments) to the new names. Stale path strings inside the renamed notes may be updated
   only when the user asked for it.
4. **Write `00_README.md`** per the structure above.
5. **Verify.**
   - Every `](…)` link in the record resolves from the topic directory.
   - Quantitative results have source support, and every cited `§x.y`, `Figure N` and `Eq. (An)` exists.
   - Every embedded figure has a caption and sits beside its relevant discussion. Numbers are
     unique; preserve existing numbers on refresh, including gaps left by removals.
   - Optionally, dry-run `md_to_slides.py 00_README.md --section "1 Summary" -o <scratchpad>/check.md`: it must exit 0.
6. **Link the record** as the first entry for the topic in the parent index (for example
   `docs/README.md`).
7. **Report:** record path, rename table, alias symlinks created, references updated, references
   deliberately left (and why), conflicts and stale items found.

## Project mode

Trigger: "project progress", "overview of all topics". Refresh each topic record first, then
update the project `README.md` (L1; never a separate STATUS.md) built **only from the
records' Section 1 and the staleness check**, in the L1 README layout (see `init-project-structure`):

- Header: date, one-line current stage, current definitions/windows table.
- Per topic, one block: link to its record, 1 key figure + caption, then four one-line bullets —
  **Done**, **Finding**, **Problem** (incl. [stale] items), **Next**.
- Then one table of open decisions/next work across topics (ID, task, status, dependency).
- No Section 2 detail, no new interpretation. Listing work does not authorize it.

## Safety

- Never delete a note, figure or symlink unless the user asks. Retiring content means linking it
  from Section 2 with its status.
- Never edit the content of `records/`, paper or manuscript repositories, or paper-locked sources.
- Never regenerate or move figures.
- Rename only inside the topic folder the user named.

---
name: notes-organizer
description: "Maintain project-level research notes during project setup or reorganization. Use for the RESEARCH_NOTES.md index and its per-topic notes/ files. Propose a bounded diff before writing. Organize existing facts without adding scientific interpretation."
---

# Organize Project Research Notes

## Purpose

Use this stage 0 skill to maintain the project-wide notes system during setup or reorganization.
Use `init-project-structure` for the project README, outlines and roadmaps,
and `0-explore-data` for read-only inspection of project inputs and outputs.
Use `4b-topic-record` when the requested deliverable is a topic's `00_README.md`.

The project notes system contains:

- `RESEARCH_NOTES.md` — master index (2–3 line summaries per topic + links to sub-notes). Short enough to read at a glance.
- `notes/<topic>.md` — per-topic detail files (figures, stats, methodology, config parameters). Read selectively by `5-ppt-design` and `4c-report`.

`RESEARCH_NOTES.md` is a **living document**: the user requests updates frequently as files are added, moved, or removed. The skill must detect what changed since the last update and propose targeted edits — not regenerate from scratch.

---

## When To Use

Trigger when prompts include:
- "organize research notes"
- "update RESEARCH_NOTES"
- "compile research progress"
- "add [topic] to the notes"
- "notes are out of date"
- "create research memo"
- "maintain research documentation"

---

## File Structure

```
<target_directory>/
  RESEARCH_NOTES.md          ← master index only (short summaries + links)
  notes/
    <topic_a>.md             ← full detail: figures, stats, config, methodology
    <topic_b>.md
    ...
```

### RESEARCH_NOTES.md — Index Format

```markdown
---
title: "[Project] Research Notes"
project: [project name]
author: [optional]
last_updated: YYYY-MM-DD
status: living document
---

# [Project] Research Notes

## Introduction
- Background: 1–2 sentences on scientific context.
- Research questions: bullet list.
- Data sources: dataset names, variables, time range.

## Methodology
1–2 paragraph prose summary. [Full methodology →](./notes/methodology.md)

## Topics

### [Topic A]
2–3 sentence summary of current status and key findings.
[Full notes →](./notes/topic_a.md) | Figures: N | Last updated: YYYY-MM-DD

### [Topic B]
...

## Appendix
- [Historical memos →](./notes/memos.md)
- [Background / glossary →](./notes/background.md)
```

### `notes/<topic>.md` — Detail Format

```markdown
# [Topic A] — Detail Notes
*(last updated: YYYY-MM-DD)*

## Experimental Design
Objective, hypothesis, input data (dataset, variables, time range, grid), method overview.

## Parameter Configuration
| Parameter | Value | Source |
|-----------|-------|--------|
| ... | ... | config.yaml |

## Precautions / Known Issues
- bullet list of caveats, edge cases, known bugs

## Results

![Short description](../figures/topic_a_result.png){width="90%"}
*Figure 1: [experiment] — what it shows.*

## Recorded Interpretation
Include only interpretation already supplied by the user or an explicitly approved source. Otherwise write: `No scientific interpretation requested.`
```

---

## Scope

**In scope:**
- Reading `.md`, `.yaml`, `.json`, `.cfg`, `.toml`, `.py` (docstrings + first 50 lines only) files in the target directory
- Scanning figure files (`.png`, `.pdf`, `.svg`) for inventory
- Writing `RESEARCH_NOTES.md` and `notes/*.md` files in the target directory

**Out of scope — never open:**
- `.nc`, `.zarr`, `.npy`, `.npz`, `.h5`, `.hdf5`, binary files
- Any file outside the target directory
- Inferring physical meaning, deciding whether a hypothesis is supported, or adding scientific conclusions

---

## Workflow

### PHASE 1 — Diff & Propose (no file creation)

**Step 1 — Read existing state**

Priority order for reading:
1. `RESEARCH_NOTES.md` if it exists — extract the current topic list and last-updated dates
2. Existing `notes/*.md` files — note their last-modified timestamps
3. Recent memoranda (by filename date or mtime)
4. Config files (`.yaml`, `.toml`, `.cfg`) closest to experiment dirs
5. Python/Fortran scripts: docstrings and first 50 lines only — skip full implementation

**Step 2 — Diff against current directory**

Compare current directory state against what is recorded in `RESEARCH_NOTES.md`:
- **New**: files/figures/experiments present in directory but not in the index
- **Changed**: files modified more recently than the sub-note's `last updated` date
- **Removed**: entries in the index whose source files no longer exist
- **Unchanged**: topics where no source files changed — skip these

**Step 3 — Present diff summary**

```
# RESEARCH_NOTES — Proposed Changes

## New items (not in current index)
- notes/drought.md — new experiment results in tests/drought_index/
- 3 new figures in figures/drought/ since last update

## Changed items (source files updated since last note)
- notes/monsoon.md — RESEARCH_NOTES §Monsoon last updated 2026-03-15; 2 source files newer

## Removed items (source files no longer present)
- notes/old_experiment.md — referenced directory deleted

## Unchanged (skipping)
- notes/validation.md — no changes detected

## Proposed sub-note files to create/update
- CREATE notes/drought.md
- UPDATE notes/monsoon.md (sections: Results, Result Analysis)
- DELETE reference to old_experiment in RESEARCH_NOTES.md

## Proposed RESEARCH_NOTES.md changes
- Add §Drought section (new)
- Update §Monsoon summary (results changed)
- Remove §Old Experiment entry
```

4. **Wait for explicit user approval** before Phase 2.

---

### PHASE 2 — Write (only after explicit user approval)

**Write order:**
1. Create or update targeted `notes/<topic>.md` files (only those in the approved diff)
2. Update `RESEARCH_NOTES.md` index (only the changed entries)

**If `RESEARCH_NOTES.md` does not exist:** write it from scratch using the index format above.

**If a `notes/<topic>.md` already exists:** update only the sections that changed — preserve all other content exactly.

#### Figure embedding rules

- **PNG**: embed inline — `![short_description](../figures/figure_name.png){width="90%"}`
- **PDF / SVG**: text link — `[View figure](../figures/figure_name.pdf)` + `<!-- PDF: inline preview not supported -->`
- Below each figure: `*Figure N: [experiment] — [what it shows, 1 sentence]*`
- Missing / unconfirmed path: `![MISSING](../figures/PLACEHOLDER.png)` + `<!-- TODO: verify path -->`

#### Detecting figure aspect ratio (for width hints)

```bash
python3 -c "from PIL import Image; w,h=Image.open('path.png').size; print(w/h)"
```
- Ratio ≥ 2.5 → `{width="95%"}` (wide landscape)
- Ratio 1.0–2.5 → `{width="90%"}`
- Ratio < 1.0 → `{width="70%"}` (portrait)

---

## Safety Constraints

- Phase 1 is strictly read-only — no files written.
- Phase 2 activates only after explicit user approval of the diff.
- **Never delete source `.md` files** (memoranda, experiment notes).
- **Never modify existing files** other than `RESEARCH_NOTES.md` and `notes/*.md`.
- **Never write outside the target directory.**
- Do not run model code or heavy computations.
- Organise observed facts separately from interpretation. Never infer or append scientific meaning unless the user explicitly requests a separate `4c-report` stage.
- When in doubt about whether to include something, keep it.

---
name: noteit
description: "Log research insights and work-progress records into a project-named notes file under ./docs and, optionally, a global Obsidian-compatible quick-notes file. Use only when the user explicitly says log note, log this, save this insight, note that, or add to research log. Prepend new entries."
---

# Research Note Logger

## Purpose

Preserve the *why* and *what-it-means* from plan-mode reasoning and Q&A threads — the dense conceptual understanding that evaporates fast during vibe-coding. Write it down at the moment of insight, in the language of physics and numerics, so it stays findable weeks later.

**Key principle: distil, don't transcribe. Capture the settled conclusion and the logical path to it, not the exploration steps.**

## When To Use

Trigger this skill when the user says any of:
- `log note`
- `log this`
- `save this insight`
- `add to research log`
- `note that [...]`
- At the end of a plan-mode investigation, if the user asks to preserve the reasoning

## What To Log

**YES — capture these:**
- Physical or numerical facts with their reasoning (e.g., "Monthly precipitation anomalies must use a per-calendar-month climatology; subtracting one annual mean leaves the seasonal cycle in the anomaly series")
- Questions that any researcher working on this topic would plausibly ask
- Hypotheses and the evidence for/against them
- Conclusions from Q&A threads (the question + the settled answer)
- Assumptions that were validated or rejected, and why
- Failure modes and why they occur
- Conceptual links between ideas (e.g., "A linear trend fitted to a series with a step change in instruments mixes the trend with the step; test for breakpoints first")

**NO — skip these:**
- Code syntax, API details, how to call a specific function
- Debugging steps that are not conceptually generalisable
- Intermediate exploration that didn't converge to a conclusion

---

## Output Files

Two files are written on every `log note` trigger.

### File 1 — Project Notes (detailed)

| Property | Value |
|----------|-------|
| **Location** | `./docs/` in the current workspace root |
| **Filename** | `<PROJECT_NAME>_notes.md` — one file per project, grows indefinitely |
| **Example** | `docs/PRECIP_TRENDS_notes.md` |

PROJECT_NAME is the workspace folder name (e.g., `PRECIP_TRENDS`).

Create `./docs/` if it does not exist. Create the file if it does not exist with a single header line `# Research Notes — <PROJECT_NAME>`. **New entries are prepended: inserted directly below the header line, above all prior entries.**

### File 2 — Global Quick Notes (optional; short / Obsidian-compatible)

| Property | Value |
|----------|-------|
| **Location** | `~/docs/quick_notes.md` |
| **Purpose** | One-liner progress records, sortable by project and date in Obsidian |

Write this file only if `~/docs/quick_notes.md` already exists or the user asks for a global quick-notes file; then create `~/docs/` and the file (header `# Quick Notes`) as needed. **New entries are prepended: inserted directly below the header line.**

---

## Entry Format — quick_notes.md

One line per entry, prepended below the `# Quick Notes` header:

```
## [YYYY-MM-DD] [[PROJECT_NAME]] <Short declarative claim>
```

**Concrete example:**
```
## [2026-05-28] [[PRECIP_TRENDS]] Monthly anomalies need a per-calendar-month climatology
```

Rules:
- Date in ISO format `YYYY-MM-DD` so Obsidian date-sort works correctly.
- `[[PROJECT_NAME]]` is an Obsidian wiki-link — enables backlinks from the project page and project-based filtering.
- The claim must be declarative (a fact or action, not a label).

---

## Entry Format — Project Notes File (`<PROJECT_NAME>_notes.md`)

Prepend entries using this exact template (new entries go directly below the file header line, above all prior entries). The topic title **must be a declarative claim**, not a question.

## [YYYY-MM-DD] <Topic title — a short declarative claim>

**Context / Question**
One or two sentences: what problem was being investigated or what question was asked.

**Reasoning**
The logical chain: physical/numerical logic, assumptions, sign conventions, grid conventions — whatever is needed to follow the argument. Write as prose, 3–10 sentences. Be precise about quantities and conventions.

**Conclusion**
The settled fact or validated hypothesis. Self-contained enough to be understood without re-reading the full thread.

**Source context** *(omit if obvious)*
Where this came from: "plan mode analysis of [topic]", "Q&A — [date]", "from reading [paper/doc]"

**Topic title rules:**
- Bad: "anomaly question" → Good: "Monthly anomalies need a per-calendar-month climatology"
- Bad: "trend investigation" → Good: "Station precipitation trends flip sign after the 1998 gauge change"
- Bad: "debugging regridding issue" → Good: "Bilinear regridding of precipitation does not conserve the area total"

---

## Behaviour by Trigger

### Writing procedure (applies to all triggers)

For every `log note` trigger, write the project notes file, then the quick-notes file when it is enabled (see File 2):

**Step A — Project Notes file (`./docs/<PROJECT_NAME>_notes.md`)**
1. If the file does not exist: create `./docs/` if needed, create the file with header `# Research Notes — <PROJECT_NAME>`, then write the detailed entry immediately below.
2. If the file exists: read the entire file. Write back: (header line) + blank line + (new detailed entry) + blank line + (all prior content after the header line).

**Step B — Quick Notes file (`~/docs/quick_notes.md`, only when enabled)**
1. If `~/docs/` does not exist: create it.
2. If the file does not exist: create it with header `# Quick Notes`, then write the one-line quick entry immediately below.
3. If the file exists: read the entire file. Write back: (header line) + blank line + (new one-line entry) + blank line + (all prior content after the header line).

After writing, report the file path(s) written. Do not ask for confirmation unless the topic is ambiguous.

### `log note` / `log this` / `note that [...]`
Triggered mid-conversation after a Q&A exchange.

1. Infer the topic from the most recent exchange. If the topic is unambiguous, **write immediately without asking for confirmation** — report the file path(s) after writing.
2. If the topic is ambiguous (multiple threads active), ask: *"Which topic should I log — [A] or [B]?"* — one short question, then write.

### Plan-mode extraction
Triggered when the user asks to preserve reasoning from a plan-mode investigation.

1. Extract the key conceptual findings from the plan reasoning — skip the step-by-step task decomposition.
2. Write directly without asking for confirmation.

---

## Example Entry

```markdown
---
## [2026-05-21] Bilinear regridding of precipitation does not conserve the area total

**Context / Question**
The basin-mean precipitation from a 0.25° product dropped by about 4% after regridding to 1°. Was this a data problem or a processing artefact?

**Reasoning**
Bilinear interpolation samples the source field at target-cell centres; it does not integrate the flux over each target cell. Precipitation is a flux per unit area with strong small-scale extremes, so point sampling misses part of the heavy-rain area and the area-weighted total changes. A conservative (first-order area-weighted) remapping integrates the overlap between source and target cells and preserves the total by construction. Repeating the step with conservative remapping restored the basin mean to within 0.1%, which confirmed the artefact came from the interpolation method.

**Conclusion**
Regrid precipitation and other fluxes with conservative remapping when totals or means matter. Bilinear interpolation is acceptable only for smooth state variables or for plotting.

**Source context**
Plan mode analysis of a regridding artefact, 2026-05-21
```

## Example Quick-Note Entry

```markdown
## [2026-05-28] [[PRECIP_TRENDS]] Monthly anomalies need a per-calendar-month climatology
```

---

## Notes on Design Choices

- **Single file per project** accumulates all notes for that workspace in one place. Prepending keeps the most recent entry at the top — opening the file immediately shows the latest work.
- **Global quick_notes.md** acts as a cross-project diary. Obsidian wiki-links (`[[PROJECT]]`) allow filtering by project via backlinks without any folder structure.
- **Prepend requires reading the existing file once per write.** For `quick_notes.md`, which accumulates cross-project entries over time, this cost is acceptable. If the file becomes very large, older entries can be manually archived to a dated section at the bottom.
- **Declarative topic titles** make the file scannable as a flat list of facts — useful for ctrl-F and for building a mental map of what has been established.
- **Prose reasoning** (not bullets) forces a complete logical argument rather than a list of disconnected observations.

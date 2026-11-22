# Geoscience Research Workflow Skills

**Bounded agent skills for planning, executing, documenting, and presenting computational geoscience research.**

Author: Sukun Cheng ([@Sukun](https://github.com/Sukun))

The skills target Python/xarray/NetCDF research projects and work in Claude Code, Codex, and
compatible VS Code agent-skill installations. Each skill is a folder with a `SKILL.md`, invoked as `/<skill-name>`.

## Design

- Planning creates an approval-gated `TASK_CONTRACT.md` with explicit **Non-Goals** and a
  numeric **Scope Budget**.
- Execution accepts only an approved, validated contract; it does not silently repair,
  generalize, refactor, add figures, or explain results.
- Scientific interpretation belongs to an explicit `4c-report` stage after validation.
- Slides are rendered mechanically from an approved storyboard; hand edits made in
  PowerPoint flow back into the storyboard and a layout file.

## Workflow

```
Stage 0 · Project setup and organization
  init-project-structure · 0-explore-data · notes-organizer

Execution
  1-plan-task → [2-review-plan] → 3-conduct-plan

Results
  4a-quickview → 4b-topic-record
  4c-report for scientific interpretation after validation

Slides
  5-ppt-design → 6-ppt-create → 7-ppt-to-5 after manual edits
```

| Skill | Purpose | Main output |
|---|---|---|
| `init-project-structure` | Organize research project documentation | Project README, outline or roadmap |
| `0-explore-data` | Read-only, bounded project/data inspection | Facts and unresolved gaps |
| `notes-organizer` | Maintain project research notes during setup or reorganization | `RESEARCH_NOTES.md` and `notes/<topic>.md` |
| `1-plan-task` | Convert a multi-step request into an approval-gated contract | `TASK_CONTRACT.md` |
| `2-review-plan` | Optional review for high-risk methods or compute | Verdict and blockers |
| `3-conduct-plan` | Execute an approved contract without scope expansion | Declared outputs, `RUN_RECORD.json`, `VALIDATION.md` |
| `4a-quickview` | Assemble existing figures and optionally render slides | Quickview Markdown or PPTX |
| `4b-topic-record` | Consolidate existing results by topic | Topic `00_README.md` |
| `4c-report` | Explicit scientific reporting and interpretation | Approved report draft |

### Slides and utilities

| Skill | Purpose | Main output |
|---|---|---|
| `5-ppt-design` | Decide a slide story and storyboard | Storyboard Markdown |
| `6-ppt-create` | Mechanically render an approved storyboard (direct python-pptx engine) | PPTX + `<name>.layout.json` |
| `7-ppt-to-5` | Read a hand-edited deck back: text into the storyboard, positions into the layout file | Updated storyboard and `<name>.layout.json` |
| `noteit` | Log an explicitly requested fact or decision | Project note entry (optional global quick note) |

## Safety model

For a Level B or C task, `1-plan-task` must define deliverables, input paths,
**Non-Goals**, allowed changed files, success criteria, stop conditions, and a Scope
Budget. The default Level B budget permits one primary experiment and one sanity check,
but no parameter variants, extra figures, extra reports, dependencies, refactors, or
scientific interpretation.

`3-conduct-plan` checks approval and contract validity before it writes or runs.
It first runs the smallest approved validation and stops for failed validation, a method
change, a budget expansion, a new output, a destructive action, or an unclear allowed
path. Its records contain observable facts only.

Call `4c-report` explicitly when you want an interpretation or scientific explanation.
It separates observed results from interpretation and does not run new analyses by
default.

## Install

Clone the repository and link the desired folders from `skills/` into your client's
skills directory.

```bash
git clone https://github.com/Sukun/geoscience-research-workflow-skills.git
cd geoscience-research-workflow-skills
```

With a whole-directory symlink, install `skills/` as the target of
`~/.claude/skills`. Back up an existing installation before switching. Edit the
files under `skills/` and both paths will see the same changes.

Several skills apply a personal writing- or code-preferences file if you keep one; none is
required.

Common locations are `~/.claude/skills/` for Claude Code and `~/.agents/skills/`
for Codex. VS Code agent-skill discovery depends on the installed extension and
workspace settings; consult that client's documentation. Keep the folders intact:
each `SKILL.md` uses relative links to its bundled resources.

For `6-ppt-create`, `7-ppt-to-5` and `4a-quickview` slide rendering, install `python-pptx` and
Pillow (Pandoc is not needed). The PPT tests use `pytest`.

## Validate locally

```bash
python3 tools/verify_skill_tree.py skills
python3 -m pytest skills/6-ppt-create/tests skills/7-ppt-to-5/tests
python3 skills/1-plan-task/scripts/validate_task_contract.py \
  /path/to/TASK_CONTRACT.md --require-approved
```

The first command checks frontmatter, directory/name matching, resource links, and
machine-specific paths. The second runs the slide-engine tests. The third validates an execution contract.

## Example

See [the synthetic precipitation example](examples/demo-precip-trend/README.md). It
shows the separation between a proposed contract, execution facts, and a later
interpretation—not a real dataset or a private project.

## Non-goals

- This is not autonomous research or unattended HPC execution.
- It does not decide research questions or create scientific claims without review.
- It does not install editor extensions, manage credentials, or publish results.

## License

MIT © Sukun Cheng — see [LICENSE](LICENSE).

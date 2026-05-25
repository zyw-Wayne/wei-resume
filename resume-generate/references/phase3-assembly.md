# Phase 3 - Assembly + Validation Rules

## Output Directory Structure

All output files are organized by target and template:

```
~/resumes/output/<target>/<template>/
  full.md, full.html
  tech.md, tech.html
  hr.md, hr.html
~/resumes/output/<target>/
  match-report.md (if target provided)
  resume-plan.json
```

- `<target>`: the target identifier (e.g., `bytedance-fe`, `alibaba-senior-fe`). Use `_general/` when no target is specified.
- `<template>`: the template name (e.g., `classic`, `modern`).

Each subagent assembles its own style's `.md` + `.html` files and writes them directly to the correct subdirectory.

## Assembly Order

- Follow `resume-plan.json` `sections[].order` strictly
- Concatenate each `sections/<id>.md` in order with appropriate section separators
- Apply template header/footer if format is HTML

## "Other Projects" Assembly

After all selected project sections are assembled, append a one-liner "其他项目" section listing remaining projects from `resume-plan.json.other_projects[]`:

```markdown
### 其他项目

- **项目名称A** -- 简要标签描述
- **项目名称B** -- 简要标签描述
```

Each entry uses the `one_liner` field from the plan. This section does not count toward the main projects budget but must fit within the total page budget.

## Consistency Checks

1. **Terminology unification**: same technology must use consistent naming (e.g., "TypeScript" everywhere, not "TS" in one place and "Typescript" in another)
2. **No duplicate achievements**: verify projects and experience sections don't repeat the same story or metric; if overlap detected, keep the stronger version and trim the weaker
3. **Timeline logic**: work history dates and project dates must not contradict (project dates should fall within or near corresponding employment periods)

## Density Checks

- Calculate total word/character count vs page budget from resume-plan.json
- If content exceeds budget, trim in priority order:
  1. `extras` section (certifications, languages, interests)
  2. Education details (keep degree + school, trim coursework/GPA)
  3. Low-score value_points (from resume-plan.json overflow list)
  4. 4th and subsequent bullets in any single role/project
- Never trim below minimum: summary (2 lines), each role (2 bullets), each project (2 bullets)

## Format Output

- **.md**: Direct assembly of sections, no additional processing
- **.html**: Wrap assembled content in template `styles.css`, produce self-contained HTML with embedded CSS; no external dependencies

## ATS Version (if --ats flag)

- Strip all formatting: no bold, italic, links, tables
- Plain text structure with clear section headers
- Ensure target keywords are naturally distributed throughout (not keyword-stuffed)
- Use standard section titles ("工作经历" not "我的旅程"; "Work Experience" not "My Journey")
- Contact info on separate lines (not comma-separated)
- Generate `ats-report.md`:
  - Keyword hit rate (matched / total required keywords)
  - Missing keywords list
  - Pass probability estimate (high / medium / low)

## Quality Scoring (0-100)

Score is computed across five dimensions, each weighted equally (20 points max):

1. **Quantification coverage** (20): bullets with numbers / total bullets
2. **Keyword hit rate** (20): matched target keywords / total target keywords (if no target, score based on industry-standard terms)
3. **Structure completeness** (20): all planned sections have content and meet minimum line count
4. **Wording quality** (20): no weak verbs, no filler phrases, action verb coverage
5. **Page utilization** (20): appropriate density (not too sparse, not overflowing)

Final score = sum of dimension scores. Score is recorded per output file in the output metadata.

## Output Files

```
~/resumes/output/<target>/<template>/
  full.md, full.html
  tech.md, tech.html
  hr.md, hr.html
~/resumes/output/<target>/
  match-report.md (if target provided)
  resume-plan.json
```

- `ats-report.md` generated alongside style files if `--ats` flag is set
- Update `state.json.outputs[]` with generated file paths and timestamps

## Section Hash Recording

- After assembly, compute hash of each section's content
- Write to per-target-per-template edits file: `~/resumes/output/<target>/<template>/edits.json`
  ```json
  {
    "<section_id>": { "hash": "<sha256>", "generated_at": "..." }
  }
  ```
- On next `wei-resume edit`, compare current file hash vs recorded hash
- If hashes differ -> section was manually edited -> respect `--preserve-edits`

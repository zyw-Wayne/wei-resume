# Phase 3 - Assembly + Validation Rules

## Output Directory Structure

All output files are organized by target and template:

```
~/resumes/output/<target>/
  standard/                            # 3 variants × 3 styles
    modern/
      full.md, full.html
      tech.md, tech.html
      hr.md, hr.html
    classic/
      ...
    compact/
      ...
  academic/                            # academic × 3 styles
    full.md, full.html
    ...
  kami/                                # kami (no style variants)
    resume.md
    resume.html
    resume.pdf
  match-report.md (if target provided)
  resume-plan.json
```

- `<target>`: the target identifier (e.g., `bytedance-fe`, `alibaba-senior-fe`). Use `_general/` when no target is specified.
- Standard template has 3 variants (modern/classic/compact), each with 3 styles (tech/hr/full).
- Academic and kami have no variants.

Each subagent assembles its own style's `.md` + `.html` files and writes them directly to the correct subdirectory.

## Assembly Order

- Follow `resume-plan.json` `sections[].order` strictly
- Concatenate each `sections/<id>.md` in order with appropriate section separators
- Apply template header/footer if format is HTML

## "Other Projects" Assembly

After all selected project sections are assembled, append a one-liner "其他项目" section listing remaining projects from `resume-plan.json.other_projects[]`:

**Standard templates** (modern, classic, compact, academic):
```markdown
### 其他项目

- **项目名称A** -- 简要标签描述
- **项目名称B** -- 简要标签描述
```

**Kami template** (inline, comma-separated):
```
**其他项目：** 项目A（技术栈）· 项目B（技术栈）· 项目C（技术栈）
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
- **.html (standard/academic)**: Wrap assembled content in template `styles.css`, produce self-contained HTML with embedded CSS. See Standard Assembly Path below.
- **.html (kami)**: Template-filling assembly — see Kami Assembly Path below

### Standard Assembly Path (CSS Variant Switching)

Standard template uses one HTML + CSS with body class switching for 3 variants:

1. **Load template**: Read `templates/standard/template-{cn|en}.html`
2. **Inline CSS**: Replace the `<style>` placeholder with the full content of `templates/standard/styles.css`
3. **Set variant**: Replace `{{VARIANT}}` in `<body class="{{VARIANT}}">` with `modern`, `classic`, or `compact`
4. **Fill content**: Generate section HTML from markdown, following the class vocabulary (`.entry`, `.entry-bullets`, `.skill-badge`, etc.)
5. **Skills display**: variant determines which skills section is visible:
   - `modern`: `.skill-group` + `.skill-badges` (left column) — generate badge spans
   - `classic`: `.skills-list` (inline list) — generate `<li>` items
   - `compact`: `.skills-list` (comma-separated) — same `<li>` but CSS adds commas
6. **Layout**: CSS automatically handles two-column (modern) vs single-column (classic/compact)
7. **Hidden sections**: classic/compact hide `.column-left` via CSS; compact hides `.extras-section`

Academic template follows the same path but uses `templates/academic/` directory and has additional sections (publications, research).

### Kami Assembly Path (Template-Filling)

When `--template kami` is selected, Phase 3 uses a fundamentally different assembly approach:

**Instead of generating HTML from scratch, fill a pre-built template.**

1. **Load template**: Read `templates/kami/template-{cn|en}.html`
2. **Extract data from sections**: For each section's markdown output, extract structured content from `<!-- KAMI:xxx -->` markers:
   - Header section → name, alias, role, contact fields, metrics (×4), timeline (×3)
   - Summary → summary paragraph text
   - Experience section → date range metadata
   - Projects section → project name, type, role, role-text, actions-text, impact-text per project; other-projects list; team-culture (optional)
   - Open-source section → intro, total stars, per-project items (skip entire section if empty)
   - AI-capability section → 3 conviction cards: year + title + body per card (skip if AI level = 0)
   - Influence section → handle strip, articles (1-3), talks (1-3) (skip if both empty)
   - Skills section → 5 label + description pairs
   - Education section → school, college, major, dates, judgment note
3. **Fill placeholders**: Replace `{{PLACEHOLDER}}` tokens in template HTML with extracted content
4. **Generate dynamic blocks**:
   - For each selected project (from resume-plan.json): generate one `<div class="project">` block
   - If 5+ projects: add `class="resume--dense"` to `<body>`
   - For conditional sections (open-source, ai-capability, influence): uncomment the HTML block if data exists, delete if empty
5. **Generate "Other Projects"**: If `resume-plan.json.other_projects[]` is non-empty, generate the `.other-projects` div
6. **Validate**: No remaining `{{` in output; all `break-inside: avoid` elements intact
7. **Write output**: Self-contained HTML with all CSS inlined (CSS is already in the template's `<style>` block)

**PDF generation for kami:**
```bash
# Requires: pip install weasyprint
python3 -c "from weasyprint import HTML; HTML('output.html').write_pdf('output.pdf')"
```

WeasyPrint respects `@page` rules, `break-inside: avoid`, `widows`/`orphans`, and `@font-face` declarations. This produces significantly better PDF quality than browser print-to-PDF.

**Why template-filling is better than freeform generation:**
- HTML structure is guaranteed correct (not dependent on LLM output quality)
- CSS class names always match (no silent styling breakage)
- Consistent visual output across every generation run
- Placeholder validation catches missing data before rendering

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
~/resumes/output/<target>/
  standard/
    modern/
      full.md, full.html
      tech.md, tech.html
      hr.md, hr.html
    classic/
      full.md, full.html
      ...
    compact/
      full.md, full.html
      ...
  academic/
    full.md, full.html
    tech.md, tech.html
    hr.md, hr.html
  kami/
    resume.md
    resume.html
    resume.pdf
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

---
name: resume-generate
description: Use when generating resumes from collected profile and project data. Implements a three-phase engine (planning → parallel dispatch → assembly+validation) with template×style matrix execution, contribution-weighted project selection, and manual edit preservation.
---

# resume-generate

## Overview

Three-phase resume generation engine with parallel template×style dispatch.
Phase 1 plans content and selects projects via a contribution-weighted algorithm.
Phase 2 fans out subagents (max 6 parallel) across the template×style matrix.
Phase 3 assembles and validates each variant independently.
Default output is 3-page resumes across all templates and all styles.

## Architecture

```
Phase 1: Planning Pass (once per target)
    ↓ resume-plan.json (project selection, section allocation, narrative theme)
Phase 2: Parallel Dispatch (per template×style combination, max 6 parallel)
    ↓ sections/<template>/<style>/<id>.md
Phase 3: Assembly + Validation (per combination)
    ↓ ~/resumes/output/<target>/<template>/<style>.md|.html
```

## Phase 1 - Planning Pass

**Input:** state.json + template manifests + phase1-planning.md + target detail (if any)

**Tasks:**
- Determine which sections to include and their order (based on template + data availability)
- Allocate page budget across sections (default 3 pages → per-section line targets)
- Determine narrative theme from persona.tagline + persona.career_narrative + target keywords
- Select top 6-7 projects via contribution-weighted scoring (see algorithm below)
- Assign remaining projects to "其他项目" one-liner list
- Select and assign value_points to sections (each value_point used exactly once)
- Decide tech stack sorting strategy (with target → match score; without → frequency)
- Note content structure per section type:
  - Experience sections → overview-bullets format (2-3 high-level bullets, no tech deep-dive)
  - Project sections → background-paragraph-plus-subbullets format (business context paragraph + hierarchical sub-bullets)
  - Personal projects → listed individually (not bundled)

### Project Selection Algorithm

```
score = top_value_point_score × 0.20
      + contribution_score   × 0.25
      + target_match_score   × 0.25
      + diversity_bonus      × 0.15
      + recency_bonus        × 0.15
```

**contribution_score** auto-detected from git data:

| Role | Score | Detection signal |
|------|-------|-----------------|
| Solo developer | 100 | Single committer across all files |
| Team core / rank #1 | 85 | Highest commit count in multi-author repo |
| Main developer | 70 | Top 2-3 committer, broad file coverage |
| Module owner (large commits) | 60 | High LOC in specific directories |
| Personal project | 50 | Deprioritized vs business projects |
| Maintainer | 40 | Steady low-volume commits, reviews |

Top 6-7 projects by score are selected for full treatment. Remaining projects appear as one-line entries under "其他项目".

**Output:** `resume-plan.json`

**Context budget:** ~325 lines (state.json + template manifests + planning rules + target)

See: [references/phase1-planning.md](references/phase1-planning.md)

## Phase 2 - Parallel Dispatch (Template×Style Matrix)

For each combination in the template×style matrix, dispatch a subagent (max 6 parallel):

```
templates: modern | classic | compact | academic   (or subset via --template)
styles:    tech   | hr      | full                 (or subset via --style)
```

Each subagent independently processes all sections defined in resume-plan.json:

1. Load the section plugin: `sections/<id>.md`
2. Load the plan config for this section from resume-plan.json
3. Load only the relevant state.json data slice (not the full state)
4. Load shared rules (rules-accuracy.md, rules-power-verbs.md, rules-ats.md if --ats)

**Context per section:** ~170-290 lines (isolated per section, not full resume)

**Output:** `sections/<template>/<style>/<id>.md` files (one Markdown file per section per combination)

**Kami special handling:** The kami template does not use style variants (tech/hr/full). It generates a single output per language. Phase 2 dispatches one subagent for kami (not three). The subagent uses the `full` style as baseline, with content compressed to fit 2 pages. Output is `sections/kami/full/<id>.md`.

**Plugin interface:** Each section plugin defines:
- Required data fields from state.json
- Section-specific formatting rules
- Content format (overview-bullets for experience, background-paragraph-plus-subbullets for projects)
- Bullet style: hierarchical
- Style variants (tech / hr / full) — affects paragraph focus and bullet wording, not just emphasis
- Trim priority for density overflow

## Phase 3 - Assembly + Validation

**Input:** all `sections/<template>/<style>/*.md` + `resume-plan.json` + phase3-assembly.md + template styles

**Tasks:**
- Assemble sections in planned order per combination
- Consistency check (terminology, no duplicate achievements, timeline logic)
- Density check (word count vs page budget, trim if exceeded)
- Format output (.md direct assembly, .html via template styles.css)
- ATS version generation (if --ats flag)
- Quality scoring (0-100)

**Output per combination:** `~/resumes/output/<target>/<template>/<style>.md|.html`

When no target is specified, `<target>` defaults to `_general`.

**Context budget:** ~200 lines + assembled content

See: [references/phase3-assembly.md](references/phase3-assembly.md)

## Edit Mode

Triggered by `wei-resume edit`. Supports granular re-generation without full rebuild.

### Commands

- `edit --section <name>`: reload only that section plugin + data, regenerate single section
- `edit --section <name>.<item>`: even more granular (single project, single bullet)
- `edit --replan`: redo Phase 1 → Phase 2 → Phase 3 (full pipeline)

### Manual Edit Preservation

- Manual edit detection via hash comparison in `edits.json`
- `--preserve-edits` (default): skip sections that have been manually edited
- `--force-section <name>`: override manual edits for that section (backs up to `versions/`)
- Hash is computed per-section after generation; if file hash differs from recorded hash, section is marked as manually edited
- Edit mode respects the output directory structure: `~/resumes/output/<target>/<template>/<style>.md|.html`

## Style Handling

Each style variant affects the background paragraph focus and bullet wording throughout the resume, not just emphasis level:

| Style | Background paragraph focus | Bullet wording |
|-------|---------------------------|----------------|
| `tech` | Architecture decisions, system design, performance constraints | Implementation detail, technical metrics, scale numbers |
| `hr` | Business context, stakeholder needs, delivery timeline | Business impact, collaboration scope, team leadership |
| `full` | Balanced technical + business context | Mixed technical depth and business outcomes |

Each section plugin contains rules for all three style variants. When `--style all` is specified, Phase 2 dispatches three parallel subagents per template (one per style).

## Template Manifests

All templates share common v2 defaults:

| Property | Value |
|----------|-------|
| `page_limit` | 3 (overridable via `--pages`) |
| `bullet_style` | hierarchical |
| `project_format` | background-paragraph-plus-subbullets |
| `experience_format` | overview-bullets |

**kami overrides**: `page_limit: 2` (fixed), `experience_format: three-step-timeline`, `project_format: three-part-table`, `skills_display: label-description-rows`. See `templates/kami/manifest.md`.

Available templates: `modern`, `classic`, `compact`, `academic`, `kami`. When `--template all` is specified, Phase 2 dispatches across all five templates.

### Kami Template (Premium Visual Output)

The `kami` template uses Kami's professional typesetting system — warm parchment canvas, ink-blue accent, serif-led hierarchy, WeasyPrint PDF generation. It produces the highest visual quality among all templates.

**Key differences from other templates:**
- Uses a pre-built HTML template with `{{PLACEHOLDER}}` syntax (not freeform HTML generation)
- Phase 3 fills placeholders from section data (template-filling assembly, see phase3-assembly.md)
- Strict 2-page A4 (not 3 pages) — content must be compressed accordingly
- WeasyPrint for PDF (not browser print-to-PDF)
- Dense variant (`resume--dense` class) for 5+ projects
- Conditional page 2 sections: open-source, ai-capability, influence are removed if no data

**Manifest:** `references/templates/kami/manifest.md`
**Templates:** `references/templates/kami/template-cn.html`, `references/templates/kami/template-en.html`
**Placeholder mapping:** `references/templates/kami/placeholder-mapping.md`

## Command Syntax

```
wei-resume generate [options]
  --target <name|all>   Target position/JD to tailor for (default: all; _general when no target)
  --template <name|all> modern | classic | compact | academic | kami | all (default: all)
  --style <name|all>    tech | hr | full | all (default: all)
  --pages <n>           Target page count (default: 3, overrides template page_limit; kami always uses 2)
  --project <names>     Comma-separated project filter (overrides smart selection)
  --format <type>       Output format: md | html | pdf | all (default: md+html)
  --lang <code>         Output language: zh | en | zh-en (default: zh)
  --ats                 Generate ATS-optimized version
  --redact              Redact sensitive company/project names
  --preserve-edits      Preserve manual edits (default: true)
  --color <scheme>      Color scheme for HTML output (kami ignores this — uses its own palette)
  -i                    Interactive mode (pause for confirmation at each phase)
  -o <path>             Output directory (default: ~/resumes/output/)
```

**Kami-specific notes:**
- `--pages` is ignored for kami; it always renders 2 pages
- `--color` is ignored for kami; it uses its own parchment + ink-blue palette
- `--format pdf` for kami uses WeasyPrint (requires `pip install weasyprint`)
- `--lang zh` uses `template-cn.html`; `--lang en` uses `template-en.html`

### Output Directory Structure

```
~/resumes/output/
  _general/                    # no target specified
    modern/
      tech.md
      tech.html
      hr.md
      hr.html
      full.md
      full.html
    classic/
      ...
    compact/
      ...
    academic/
      ...
    kami/                      # kami template (no style variants)
      resume.md
      resume.html
      resume.pdf               # WeasyPrint-generated PDF
  <target-name>/               # when --target is specified
    modern/
      tech.md
      ...
    kami/
      resume.md
      resume.html
      resume.pdf
```

## Interactive Mode Flow (-i)

When `-i` is passed, the generation process pauses for user confirmation at each stage:

1. **Pre-check**: Verify state.json exists, has projects and optionally targets
2. **Config**: Confirm template/style/lang/pages/format/target selection
3. **Phase 1 review**: Show resume-plan.json summary including selected projects and scores, user confirms structure
4. **Phase 2 per-section review**: After each section generates, show it to user; allow immediate re-generation with different parameters
5. **Phase 3 output**: Show final quality score and file locations for all template×style combinations

Fast mode (no `-i`) executes all steps automatically without pauses.

## Context Budget Summary

| Phase   | Typical Context | Peak Context |
|---------|----------------|--------------|
| Phase 1 | ~250 lines     | ~325 lines   |
| Phase 2 | ~170 lines     | ~290 lines (per subagent) |
| Phase 3 | ~200 lines     | ~200 + content |

Key advantage: Phase 2 fans out up to 6 parallel subagents, each processing one template×style combination with section-level context isolation. Total wall-clock time scales with section count, not with the number of output variants.

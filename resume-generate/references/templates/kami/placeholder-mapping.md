# Kami Template Placeholder Mapping

How Phase 3 maps code2resume section output to kami template placeholders.

## Data Flow

```
Phase 2 output (markdown sections with KAMI markers) → Phase 3 extracts data → fills {{PLACEHOLDER}} in template HTML
```

Phase 3 reads each section's markdown, extracts structured content from `<!-- KAMI:xxx -->` markers, and injects into the template. The template HTML is the single source of truth for visual structure.

## Placeholder Convention

All placeholders use `{{UPPER_SNAKE_CASE}}` in both CN and EN templates. The template HTML is the canonical reference — this document maps each placeholder to its data source.

## Header Placeholders

| Placeholder | Source | Extraction |
|---|---|---|
| `{{NAME}}` | state.json.profile.name_zh (CN) / name_en (EN) | Direct |
| `{{ALIAS}}` | state.json.profile.alias | Direct, empty string if absent |
| `{{ROLE_TITLE}}` | state.json.persona.tagline | Direct |
| `{{GITHUB_URL}}` | state.json.profile.github_url | Direct |
| `{{GITHUB_ID}}` | Extracted from URL | Parse last path segment |
| `{{X_URL}}` | state.json.profile.x_url | Direct |
| `{{X_ID}}` | Extracted from URL | Parse last path segment |
| `{{PHONE}}` | state.json.profile.phone | Direct (CN only) |
| `{{EMAIL}}` | state.json.profile.email | Direct |
| `{{AGE}}` | state.json.profile.age | Direct (CN only) |
| `{{CITY}}` | state.json.profile.city | Direct |
| `{{COUNTRY}}` | state.json.profile.country | Direct (EN only) |

## Meta Placeholders

| Placeholder | Source | Notes |
|---|---|---|
| `{{DESCRIPTION}}` | Phase 3 generates: one sentence ≤150 chars from summary | HTML `<meta>` |
| `{{KEYWORDS}}` | Phase 3 generates: 3-5 keywords from title + sections | HTML `<meta>` |

## Metrics Placeholders (×4)

Selected in Phase 1 planning: 1 time + 1 scale + 2 results.

| Placeholder | Source |
|---|---|
| `{{M1_NUM}}` + `{{M1_UNIT}}` + `{{M1_LABEL}}` | resume-plan.json metrics[0] |
| `{{M2_NUM}}` + `{{M2_UNIT}}` + `{{M2_LABEL}}` | resume-plan.json metrics[1] |
| `{{M3_NUM}}` + `{{M3_UNIT}}` + `{{M3_LABEL}}` | resume-plan.json metrics[2] |
| `{{M4_NUM}}` + `{{M4_UNIT}}` + `{{M4_LABEL}}` | resume-plan.json metrics[3] |

**Vertical variant**: If any label exceeds 18 chars (CN) / 6 words (EN), or 3+ metrics have unequal label lengths, add `metrics--stack` class to the `.metrics` div.

## Summary Placeholder

| Placeholder | Source |
|---|---|
| `{{SUMMARY}}` | Phase 2 output: header.md → `<!-- KAMI:SUMMARY -->` block |

**Length**: max 80 chars CN / max 50 words EN (~2 lines). Not the standard 3-4 lines.

## Experience Section

### Date Range

| Placeholder | Source |
|---|---|
| `{{EXPERIENCE_DATE_RANGE}}` | state.json.experience.date_range, e.g., "2019 - 至今（8 年）" |

### Timeline Placeholders (×3)

3-step career arc (foundation → inflection → present), NOT job chronology. Extracted from header.md `<!-- KAMI:TIMELINE -->` block.

| Placeholder | Source |
|---|---|
| `{{TL1_YEAR}}` + `{{TL1_TITLE}}` + `{{TL1_DESC}}` | Timeline step 1 |
| `{{TL2_YEAR}}` + `{{TL2_TITLE}}` + `{{TL2_DESC}}` | Timeline step 2 |
| `{{TL3_YEAR}}` + `{{TL3_TITLE}}` + `{{TL3_DESC}}` | Timeline step 3 |

## Project Placeholders (×N, 3-5 on page 1)

Extracted from projects.md `<!-- KAMI:ROLE -->`, `<!-- KAMI:ACTIONS -->`, `<!-- KAMI:IMPACT -->` markers.

| Placeholder | Source | Hard Limit |
|---|---|---|
| `{{PROJ_NAME}}` | projects.md → `### project name` | — |
| `{{PROJ_TYPE}}` | state.json.projects[].type | — |
| `{{PROJ_ROLE}}` | state.json.projects[].role | — |
| `{{ROLE_TEXT}}` | projects.md → `<!-- KAMI:ROLE -->` block | max 60 chars CN / max 40 words EN |
| `{{ACTIONS_TEXT}}` | projects.md → `<!-- KAMI:ACTIONS -->` block | max 80 chars CN / max 55 words EN |
| `{{IMPACT_TEXT}}` | projects.md → `<!-- KAMI:IMPACT -->` block | max 100 chars CN / max 65 words EN |

**Dynamic count**: Phase 3 generates N `<div class="project">` blocks from resume-plan.json (3-5). If 5+ projects, add `class="resume--dense"` to `<body>`.

**Other projects**: `{{OTHER_PROJECTS_LIST}}` — comma-separated one-liner from resume-plan.json.other_projects[]. Format: `项目A（技术栈）· 项目B（技术栈）`.

**Team culture** (optional): `{{TEAM_CULTURE_TEXT}}` — from state.json.projects[].team_culture. Remove the `.team-culture` div if empty.

## Page 2 Conditional Sections

### Open Source (skip if `state.json.open_source.projects` is empty)

| Placeholder | Source |
|---|---|
| `{{OS_DATE_RANGE}}` | state.json.open_source.date_range |
| `{{OS_SUBTITLE}}` | state.json.open_source.subtitle |
| `{{OS_POSITIONING}}` | One-sentence positioning from open-source section |
| `{{OS_DESCRIPTION}}` | Developer identity description |
| `{{STARS_TOTAL}}` / `{{FORKS_TOTAL}}` / `{{FOLLOWERS_TOTAL}}` | state.json.open_source totals |
| `{{OS1_NAME}}` / `{{OS1_DESC}}` / `{{OS1_URL}}` / `{{OS1_STARS}}` | Open-source project 1 (highest stars, `.big` class) |
| `{{OS2_NAME}}` / `{{OS2_DESC}}` / `{{OS2_URL}}` / `{{OS2_STARS}}` | Open-source project 2 (`.big` class) |
| `{{OS3_NAME}}` / `{{OS3_DESC}}` / `{{OS3_URL}}` / `{{OS3_STARS}}` | Open-source project 3 |
| `{{OS4_NAME}}` / `{{OS4_DESC}}` / `{{OS4_URL}}` / `{{OS4_STARS}}` | Open-source project 4 |
| `{{OS5_NAME}}` / `{{OS5_DESC}}` / `{{OS5_URL}}` / `{{OS5_STARS}}` | Open-source project 5 |
| `{{OS6_NAME}}` / `{{OS6_DESC}}` / `{{OS6_URL}}` / `{{OS6_STARS}}` | Open-source project 6 |
| `{{OS_HIGHLIGHT_TAG}}` | One-word tag for highlight |
| `{{OS_HIGHLIGHT_TEXT}}` | Anecdote about external validation |

**Dynamic count**: Phase 3 generates 2-6 `os-item` blocks based on data. Top 2 by stars get `.big` class.

### AI Capability (skip if `state.json.ai_capability.level == 0`)

| Placeholder | Source |
|---|---|
| `{{AI1_YEAR}}` + `{{AI1_TITLE}}` + `{{AI1_BODY}}` | Card 1 from ai-capability section |
| `{{AI2_YEAR}}` + `{{AI2_TITLE}}` + `{{AI2_BODY}}` | Card 2 |
| `{{AI3_YEAR}}` + `{{AI3_TITLE}}` + `{{AI3_BODY}}` | Card 3 |

### Influence (skip if both articles and talks are empty)

| Placeholder | Source |
|---|---|
| `{{SOCIAL_PLATFORM}}` | state.json.influence.social_accounts[0].platform |
| `{{SOCIAL_URL}}` | state.json.influence.social_accounts[0].url |
| `{{SOCIAL_HANDLE}}` | state.json.influence.social_accounts[0].handle |
| `{{SOCIAL_FOLLOWERS}}` | state.json.influence.social_accounts[0].followers |
| `{{SOCIAL_DESC}}` | Content product description |
| `{{ARTICLES_SUBTITLE}}` | Subtitle for articles block |
| `{{ART1_TITLE}}` + `{{ART1_URL}}` + `{{ART1_DATE}}` + `{{ART1_STATS}}` | Article row 1 |
| `{{ART2_TITLE}}` + `{{ART2_URL}}` + `{{ART2_DATE}}` + `{{ART2_STATS}}` | Article row 2 |
| `{{TALKS_SUBTITLE}}` | Subtitle for talks block |
| `{{TALK1_TITLE}}` + `{{TALK1_VENUE}}` + `{{TALK1_DATE}}` | Talk row 1 |
| `{{TALK2_TITLE}}` + `{{TALK2_VENUE}}` + `{{TALK2_DATE}}` | Talk row 2 |

**Dynamic count**: Template defaults to 2 rows each. Phase 3 uses 1-2 based on available data (removes unused rows). If 3+ items exist, pick the 2 most impactful.

## Skills Placeholders (×5)

Extracted from skills.md `<!-- KAMI:SKILLS -->` block.

| Placeholder | Source |
|---|---|
| `{{SKILL1_LABEL}}` + `{{SKILL1_BODY}}` | Skill row 1 |
| `{{SKILL2_LABEL}}` + `{{SKILL2_BODY}}` | Skill row 2 |
| `{{SKILL3_LABEL}}` + `{{SKILL3_BODY}}` | Skill row 3 |
| `{{SKILL4_LABEL}}` + `{{SKILL4_BODY}}` | Skill row 4 |
| `{{SKILL5_LABEL}}` + `{{SKILL5_BODY}}` | Skill row 5 |

Each body must contain at least one `<span class="em-brand">` emphasis.

## Education Placeholders

| Placeholder | Source |
|---|---|
| `{{SCHOOL}}` | state.json.education.school |
| `{{COLLEGE}}` | state.json.education.college |
| `{{MAJOR}}` | state.json.education.major |
| `{{EDU_DATE}}` | state.json.education.date_range |
| `{{EDU_NOTE}}` | state.json.education.note (optional, judgment-flavored) |

## HTML Generation Rules

Phase 3 does NOT write freeform HTML. It:

1. Reads the template HTML file (`template-cn.html` or `template-en.html`)
2. For each `{{PLACEHOLDER}}`, replaces with extracted content
3. For dynamic sections (projects, os-items, articles, talks): generates repeated HTML blocks from data, removes unused template blocks
4. For conditional sections: removes the entire `<section>` block if data is empty
5. **Page-break reassignment**: when removing the open-source section (which has `class="page-break"`), move the `page-break` class to the next visible page-2 section (ai-capability, influence, skills, or education — whichever is first present)
6. Validates: no remaining `{{` in output
7. Outputs self-contained HTML with all CSS inlined

## Quality Checks After Fill

- [ ] No `{{` placeholders remain
- [ ] Page count = 2 (verify with WeasyPrint)
- [ ] `.hl` count: max 2 per project, 1 per row
- [ ] `.em-brand` count: 1 per skill row
- [ ] All `break-inside: avoid` elements intact
- [ ] Dense variant applied if 5+ projects
- [ ] `page-break` class present on first page-2 section
- [ ] No Chinese punctuation in EN template (and vice versa)

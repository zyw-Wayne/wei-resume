# Phase 1 - Planning Rules

## Input Requirements

1. **state.json** (full): contains profile, projects, experience, education, skills, value_points, persona
2. **Template manifest**: defines sections_order, available section plugins, style defaults
3. **Target detail** (optional): JD text, target title, required skills, preferred skills, keywords

## Section List Determination

- Start from template manifest `sections_order` as the candidate list
- For each candidate section, check if state.json has corresponding data
- Skip sections with no data (e.g., skip `publications` if state.json.publications is empty)
- If target is provided, may promote/demote sections (e.g., promote `ai-capability` for AI roles)
- Final section list with order is written to resume-plan.json

## Smart Project Selection Algorithm

After determining the section list, score and rank all projects from state.json to decide which get full treatment vs. one-liner mention.

### Scoring Formula

```
score = top_value_point_score × 0.20
      + contribution_score × 0.25
      + target_match_score × 0.25
      + diversity_bonus × 0.15
      + recency_bonus × 0.15
```

### contribution_score Detection Rules

| Condition | Score | Label |
|-----------|-------|-------|
| `author_ratio > 90%` OR role contains "项目负责人/独立开发" | 100 | solo/owner |
| `author_ratio > 25%` OR commit rank #1 | 85 | team core |
| Has [主要开发] tagged modules | 70 | main dev |
| Low commit count but high line count (single commit volume large) | 60 | module owner |
| Personal projects (path starts with `github:` or `author_ratio=100%` but not a company project) | 50 | deprioritized |
| Other work projects | 40 | maintainer |

### diversity_bonus

- Penalize if same project type already selected (e.g., two "web-management-platform" types get diminishing returns)
- First project of a type: full bonus; second of same type: halved; third+: zero

### recency_bonus

- Projects from current job: +10
- Projects within 2 years: +5
- Older projects: 0

### Selection Count (Top N)

| Pages | Selected (full treatment) |
|-------|--------------------------|
| 1 | 3 |
| 2 | 4-5 |
| 3 | 6-7 |

Remaining projects are placed in `other_projects[]` for one-liner mention at the end of the projects section.

## Page Budget Allocation

- Total budget from `--pages` parameter (default: 3 pages, ~60 lines per page for zh, ~55 for en)
- Allocate percentage per section based on content weight:

| Section | Budget % | Notes |
|---------|----------|-------|
| header | 3-5% | Fixed overhead, always present |
| summary | 5-8% | Brief positioning statement |
| skills | 8-12% | Varies by display format (badges vs list) |
| experience | 12-18% | Overview only: role + 2-3 high-level bullets per job |
| projects | 35-45% | Main content: background paragraph + hierarchical bullets |
| education | 3-5% | Minimal unless academic template |
| ai-capability | 5-8% | Only when AI level >= 2 |
| extras | 2-4% | Social proof, certs, minimal space |

- Adjust weights based on career stage: junior -> more projects; senior -> more experience
- With target: boost sections matching JD emphasis keywords
- One-page mode (--pages 1): halve all bullet counts, drop extras and ai-capability if needed

## Experience Section Guidance

The experience section is **overview only** -- it answers "what was your role and overall contribution", not "what did you build technically".

- 2-3 high-level bullets per job
- Focus on: scope of responsibility, team size, business domain, key outcomes
- Avoid: technical implementation detail, architecture specifics, performance numbers (save those for projects)
- Example bullet: "Led a 6-person frontend team delivering 3 customer-facing products, reducing release cycle from 2 weeks to 3 days"
- Anti-example: "Built a React micro-frontend architecture with Module Federation and custom Webpack plugins"

Technical depth belongs in the projects section, which now carries the primary content weight.

## Narrative Theme Generation

- Combine: persona.tagline + persona.career_narrative + target keywords (if any)
- Generate a 1-sentence narrative thread that connects all sections
- Example: "Full-stack engineer with deep infrastructure experience driving reliability at scale"
- This theme guides tone and emphasis across all section plugins

## Value Point Distribution

- Each value_point in state.json.projects[].value_points is assigned to exactly ONE section
- No duplication: a value_point appears in one place only
- Assignment priority: section with strongest context for that point
- Track assignment in resume-plan.json: `sections[].assigned_value_points[]`
- Unassigned value_points are listed in `overflow_value_points` for Phase 3 trimming decisions

## Tech Stack Sorting Strategy

- **With target:** sort by match score (target-required first, target-preferred second, then frequency)
- **Without target:** sort by frequency (most-used across projects first)
- Group by category: languages -> frameworks -> infrastructure -> tools -> practices
- Record strategy in resume-plan.json for section plugins to follow

## Style Differentiation Notes

For each section, record style-specific guidance:
- **tech**: depth of implementation detail, architecture terminology, performance numbers
- **hr**: business impact framing, team/leadership language, stakeholder communication
- **full**: balanced -- technical credibility with business context

## AI Capability Level Determination

Assess level 0-4 based on profile.ai fields + projects[].summary.ai_signals:
- **Level 0**: No AI usage record -- do not create ai-capability section
- **Level 1**: Uses AI tools for coding assistance (Copilot, ChatGPT) -- add one line in skills section only
- **Level 2**: Has Prompt Engineering / Context Engineering practice (CLAUDE.md, .cursorrules, prompt templates) -- conditional section, 2-3 bullets
- **Level 3**: Has Agent / Skills / MCP development experience (custom skills, MCP servers, agent frameworks) -- include section prominently + highlight in projects
- **Level 4**: Has public output on AI topics (blog posts, open source AI tools, talks) -- prominent section + extras showcase

## Conditional Sections

- `ai-capability`: only include if AI level >= 2
- `publications` / `research`: only for academic template or if data exists
- `open-source`: only if state.json.open_source has entries with significant contributions

## Output Format: resume-plan.json

```json
{
  "generated_at": "2024-01-15T10:30:00Z",
  "narrative_theme": "...",
  "target": { "title": "...", "company": "...", "keywords": ["..."] },
  "style": "tech",
  "total_pages": 3,
  "total_lines_budget": 180,
  "ai_level": 2,
  "tech_sort_strategy": "match_score",
  "output_dir": "~/resumes/output/<target>/<template>/",
  "selected_projects": [
    {
      "project_id": "proj_001",
      "name": "...",
      "score": 82.5,
      "score_breakdown": {
        "top_value_point_score": 90,
        "contribution_score": 85,
        "target_match_score": 80,
        "diversity_bonus": 75,
        "recency_bonus": 10
      }
    }
  ],
  "other_projects": [
    {
      "project_id": "proj_008",
      "name": "...",
      "one_liner": "内部工具平台，负责权限模块开发"
    }
  ],
  "sections": [
    {
      "id": "summary",
      "order": 1,
      "plugin": "sections/summary.md",
      "lines_budget": 8,
      "assigned_value_points": ["vp_001", "vp_003"],
      "style_notes": { "tech": "...", "hr": "...", "full": "..." },
      "data_keys": ["persona", "skills.top"]
    }
  ],
  "overflow_value_points": ["vp_012"],
  "trim_priority": ["extras", "education_details", "low_score_vp", "4th_plus_bullets"]
}
```

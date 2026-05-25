---
name: resume-init
description: Use when collecting personal profile information for resume generation through structured interviews. Supports three-round interviews, quick mode, historical resume import, and incremental updates.
---

# resume-init

## Overview

Three-round structured interview that builds a comprehensive personal profile stored in `state.json`.
Each round is an independent context window that loads its own reference file plus the current `state.json`.
`state.json` is the **only** data bridge between rounds — all collected data is written there immediately after each answer, so interruptions never lose progress.

## Zero-Interview Quick Path

When no `state.json` exists and user runs with `--quick`:
1. Auto-detect `git_authors` from local git log
2. Create minimal `state.json` with detected authors and placeholder profile
3. Skip directly to `resume-scan` + `resume-generate`
4. User can always run `wei-resume init` later to enrich the profile

## Interview Rounds

### Round 1: Deterministic Information (3-5 min)
- Name, contact, links, education, career overview, git authors
- Load reference: `references/round1-basic.md`
- Output: `state.json` sections `profile.basic`, `.education`, `.career`, `.git_authors`

### Round 2: Exploratory Mining (5-8 min)
- Tech highlights, team role, proud projects, certs/OSS/articles, target direction
- Load reference: `references/round2-explore.md`
- Output: `state.json` section `profile.strengths`, `.target_direction`

### Round 3: Deep Interview (12-18 min)
- Merged personality + AI cognition, adaptive topic pools
- Load reference: `references/round3-deep.md`
- Output: `state.json` sections `profile.persona`, `.ai`

### Import Round (optional, 5-10 min)
- Historical resume import + diagnosis + follow-up questions
- Load reference: `references/import-strategy.md`
- Output: merged data in relevant `state.json` sections

## Commands

```
wei-resume init                              # Full three rounds
wei-resume init --quick                      # Only round 1 basics, then scan+generate
wei-resume init --import <file>              # Import historical resume (PDF/DOCX/MD/TXT/HTML)
wei-resume init --update                     # Add/modify existing profile interactively
wei-resume init --update --section ai        # Update only AI dimension
wei-resume init --show                       # View current profile overview
```

## Execution Rules

1. **Independent rounds**: Each round loads its own reference file + `state.json`. No cross-round memory.
2. **Immediate persistence**: Write to `state.json` after every answer — never batch writes to round end.
3. **Single data bridge**: `state.json` is the ONLY mechanism for passing data between rounds.
4. **Graceful interruption**: If user stops mid-round, all previously collected data is already saved.
5. **Incremental updates**: `--update` reads existing `state.json`, shows current values, asks what to change.
6. **Section-scoped updates**: `--update --section <name>` limits questions to that section only.

## Context Budget

Each round should stay within budget:
- SKILL.md + roundN.md + state.json ~ 250-300 lines per round
- If `state.json` grows large, load only the relevant sections for the current round

## State Schema (written by this skill)

```
state.json
├── _meta
│   ├── schema_version: 3
│   ├── created_at
│   ├── updated_at
│   └── migrations_applied[]
├── profile
│   ├── basic        (name_zh, name_en, phone, email, links)
│   ├── education[]  (school, major, degree, period)
│   ├── career[]     (company, title, period, highlights[])
│   ├── git_authors[] (用于 scan 匹配贡献)
│   ├── strengths    (tech_direction, team_role, highlights[], certifications, open_source, articles, talks)
│   ├── target_direction (direction[], company_type)
│   ├── persona      (work_style, problem_solving, collaboration, self_image[], drivers[], growth_area, career_narrative, tagline, stories[])
│   ├── ai           (tools, skills[], cognition, workflow)
│   └── social_proof[]
```

Each round writes to specific sections:
- Round 1 → profile.basic, profile.education, profile.career, profile.git_authors
- Round 2 → profile.strengths, profile.target_direction
- Round 3 → profile.persona, profile.ai
- Import → merges into any relevant section

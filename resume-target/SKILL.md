---
name: resume-target
description: Use when managing target job positions for resume customization. Handles JD parsing, match score calculation, gap analysis, and multi-target comparison for directed resume generation.
---

# resume-target

Target job position management -- parse JDs, calculate match scores, identify gaps for directed resume generation.

## JD Smart Parsing

Extract structured data from job descriptions into the following fields:

- **tech_required**: must-have technical skills (e.g. Java, Kubernetes, distributed systems)
- **tech_preferred**: nice-to-have skills (e.g. Rust, GraphQL)
- **responsibilities**: key responsibilities extracted from the JD
- **requirements**: experience and education requirements
- **keywords**: high-frequency terms for ATS keyword matching
- **level**: estimated seniority level (P5 / P6 / P7 etc.) inferred from requirements and scope
- **company**: company name

Store all parsed fields in a single target JSON object.

## Match Score Calculation

Score the user's profile against the parsed JD on a 0-100 scale with breakdown:

| Dimension            | Weight | Method                                              |
|----------------------|--------|-----------------------------------------------------|
| Tech stack overlap   | 40%    | required skills (weighted 2x) + preferred (1x)      |
| Experience alignment | 30%    | years of experience + domain match                   |
| Keyword coverage     | 30%    | JD keywords found in profile and project descriptions|

Additional outputs:

- **match_gaps[]**: list of missing or weak areas (skills, experience, keywords)
- Per-dimension sub-scores so the user can see where to improve
- Overall score with a short qualitative label (Strong / Moderate / Weak match)

## Commands

```
wei-resume target --jd "JD text"             # Direct JD input (inline text)
wei-resume target --url <job-posting-url>     # Fetch from URL (chrome-devtools MCP)
wei-resume target --import <file>             # Import JD from local file
wei-resume target -i                          # Interactive input (prompted)
wei-resume target list                        # List all saved targets
wei-resume target show <name>                 # Show target details + match score
wei-resume target remove <name>               # Delete a saved target
wei-resume target compare                     # Multi-target match comparison table
```

## URL Fetching

When `--url` is provided:

1. Use **chrome-devtools MCP** (`navigate_page` -> `take_snapshot`) to load the job posting URL.
2. Extract the main content from the page snapshot.
3. Pass extracted text through the JD Smart Parsing pipeline.
4. Save the result the same way as direct input.

## Storage

- Each target is saved to `~/resumes/targets/<name>.json` containing all parsed fields, raw JD text, match score, and match_gaps.
- A summary entry is appended to `state.json.targets[]` with: name, company, level, match_score, and timestamp.
- Target name is auto-generated from `<company>-<level>` or can be overridden with `--name`.

## Multi-Target Comparison

`wei-resume target compare` produces a comparison table across all saved targets:

| Target         | Score | Top Gaps               | Recommendation          |
|----------------|-------|------------------------|-------------------------|
| ByteDance-P7   | 82    | Rust, distributed tx   | Highlight system design |
| Alibaba-P6     | 91    | Cloud-native           | Strong match            |

Recommendations suggest which profile sections to emphasize or which gaps to address for each target.

## Context Budget

SKILL.md + state.json ~ 230 lines. Keep target JSON files outside the main context; load on demand via `target show`.

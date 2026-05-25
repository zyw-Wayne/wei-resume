---
name: resume-import
description: Use when importing external data sources to enrich resume profile. Supports GitHub profiles, tech blogs, patents, papers, LeetCode, Stack Overflow, and other platforms for social proof and additional evidence.
---

# resume-import

External data source importer that enriches state.json with social proof and additional evidence from developer platforms.

## Supported Sources and Commands

```
wei-resume import --github <username>              # GitHub profile
wei-resume import --github <username> --depth full # Deep analysis (PRs/Issues)
wei-resume import --articles <url>                 # Tech blog homepage (all articles)
wei-resume import --article <url>                  # Single article
wei-resume import --patent <patent-number>         # Patent by number
wei-resume import --paper <title> --doi <doi>      # Academic paper
wei-resume import --feedback <file>                # Peer review / performance review
wei-resume import --leetcode <username>            # LeetCode profile
wei-resume import --stackoverflow <user-id>        # Stack Overflow profile
wei-resume import --juejin <username>              # Juejin (掘金) profile
```

## GitHub Profile Extraction (--github)

Extract the following from a GitHub profile:

- **Contribution heatmap**: total activity count, consistency score, longest consecutive streak
- **Top starred repos**: name, stars, forks, description, primary language
- **PRs to other projects**: target project name, PR count, merge rate
- **Issue discussion quality**: answers given, depth of engagement
- **Language distribution**: percentage breakdown across all repos
- **Organizations**: org memberships and roles

Default mode fetches public profile + top repos. `--depth full` additionally crawls merged PRs and issue activity.

Storage: `~/resumes/github/<username>-profile.json`

## Social Proof Smart Presentation Strategy

### With a target JD

Select platform emphasis based on JD requirements:

- JD emphasizes **algorithms** -> prioritize LeetCode (ranking, solve count, contest rating)
- JD emphasizes **community influence** -> prioritize Stack Overflow + Juejin (reputation, followers, top answers)
- JD emphasizes **open-source experience** -> prioritize GitHub (stars, PRs merged to major projects, maintainer roles)
- No special emphasis -> pick the most impressive 1-2 platforms automatically

### Without a target JD

Sort by "data impressiveness" using this hierarchy:

1. Rankings and percentiles (e.g. Top 5% on LeetCode, Top 1% on SO)
2. Absolute numbers (e.g. 1200+ stars, 500+ answers)
3. Activity and consistency (e.g. 365-day streak, weekly publishing)

### Presentation Position Decision

| Tier                                        | Placement                              |
|---------------------------------------------|----------------------------------------|
| Very outstanding (Top 10% / 1000+ Stars)    | Mention in summary section             |
| Notable (meaningful but not top-tier)        | Independent line in extras section      |
| Average (present but unremarkable)           | Tag in skills section, or omit entirely |

## Data Freshness

- GitHub profile cache expires after **7 days**; re-import to refresh.
- All other sources are fetched **on demand** (no automatic cache).
- Each import records a `fetched_at` timestamp in the stored JSON.

## Web Fetching

All web page fetching (blog, LeetCode, Stack Overflow, Juejin) uses **chrome-devtools MCP**:

1. `navigate_page` to the target URL
2. `take_snapshot` to capture rendered content
3. Parse the snapshot for structured data extraction

This ensures JavaScript-rendered content (SPA pages, dynamic profiles) is captured correctly.

## Output

Imported data enriches state.json in the following locations:

- `profile.social_proof[]` — platform entries with metrics and tier classification
- `profile.strengths.open_source[]` — GitHub repos and OSS contributions
- `profile.strengths.articles[]` — blog posts, technical articles
- `profile.strengths.patents[]` — patents by number
- `profile.strengths.talks[]` — conference talks, presentations
- `projects[]` — GitHub repos may be added as project entries (with scan_params noting github as source)

Each import is idempotent: re-importing the same source updates existing entries rather than duplicating them.

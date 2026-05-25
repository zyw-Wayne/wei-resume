# Data Accuracy Rules

Strict constraints governing how scanned data is classified, stored, and later used in resume generation.

## 1. Data Source Classification

Every data point extracted during scanning MUST carry a `data_source` tag:

| Source Type | Description | Confidence | Example |
|-------------|-------------|------------|---------|
| `git_verifiable` | Directly from git commands, reproducible | HIGH | Commit count, file count, LOC added, first commit date |
| `code_inferable` | Derived from code structure analysis | HIGH | Tech stack (from imports), architecture pattern (from directory layout), framework usage |
| `git_estimated` | Computed from git statistics, involves interpretation | MEDIUM | Contribution ratio, core module ownership %, growth trajectory |
| `context_required` | Cannot be obtained from code/git, needs user input | HIGH (after confirmation) | QPS, latency numbers, SLA, business metrics, team size |
| `not_available` | Cannot be obtained or reasonably estimated | N/A | Revenue impact, user count, conversion rates (unless user provides) |

## 2. Strict Prohibition List

The following data types MUST NEVER be fabricated, estimated, or inferred. They may ONLY appear if provided by the user as `context_required` → `user_confirmed`:

- **Performance metrics**: QPS, TPS, latency (P50/P99), throughput, requests per second
- **Availability metrics**: SLA percentages, uptime (99.9%, 99.99%), MTTR, MTBF
- **Business metrics**: GMV, revenue, MAU/DAU, conversion rates, user growth
- **Specific improvement percentages**: "improved by 40%", "reduced by 60%", "3x faster"
- **Team exact headcount**: "led a team of 8" (unless git shortlog is precise AND user confirms)
- **Cost savings**: dollar amounts, infrastructure cost reduction percentages
- **Scale metrics**: "serving 10M users", "processing 1B events/day"

## 3. Placeholder Mechanism

When scanning identifies a value point that needs `context_required` data:

### Generate a Placeholder
```json
{
  "id": "api_p99_latency",
  "question": "What was the P99 API latency after your optimization?",
  "hint": "e.g., '< 50ms' or 'reduced from 200ms to 80ms'",
  "current_value": null
}
```

### Interactive Completion Flow
After scan completes, present all unfilled placeholders to the user:
1. Group placeholders by project
2. Show the value point context for each placeholder
3. Accept user input or explicit "skip" for each
4. Filled placeholders are tagged `user_confirmed` with timestamp

### Storage
- Placeholders live in `state.json.projects[].value_points[].placeholders[]`
- Filled values: `current_value` is set, `confirmed_at` timestamp added, `data_source` becomes `user_confirmed`
- Skipped values: `current_value` remains null, `skipped: true`

## 4. Generation-Phase Rules

These rules are shared with `resume-generate` and govern how data appears in the final resume:

### Rule 1: Allowed Data Sources
Only use data tagged as `git_verifiable`, `code_inferable`, or `user_confirmed` in specific claims. Data tagged `git_estimated` may be used with hedging language ("primarily responsible for", "major contributor to").

### Rule 2: Unfilled Placeholders
If a placeholder has `current_value: null`, the generation phase MUST use vague but honest wording instead of specific numbers. Example: "Significantly improved API response time" instead of "Reduced P99 latency by 60%".

### Rule 3: Allowed Reasonable Inference
Wording upgrades based on strong git evidence ARE permitted:
- "Built X from scratch" — allowed when git shows author created the directory and 80%+ of files
- "Designed and implemented Y" — allowed when author made initial commits and architectural files
- "Introduced Z to the project" — allowed when the dependency first appears in an author's commit
- "Established testing practices" — allowed when author created the first test files and CI config

### Rule 4: Before/After Constraints
The before/after analysis section may only use `git_verifiable` data (file counts, module counts, test file counts). Never use estimated percentages or inferred performance improvements in before/after claims.

### Rule 5: User-Confirmed Precision
Once a user confirms a number via placeholder completion, it may be used precisely and confidently in the resume. Tag the source as `user_confirmed` for audit trail.

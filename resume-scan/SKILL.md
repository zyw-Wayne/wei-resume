---
name: resume-scan
description: Use when scanning code repositories to extract resume-worthy content. Analyzes code structure, git history, tech stack, contributions, and generates structured project summaries with accuracy-annotated data points.
---

# resume-scan

Intelligent code repository scanner that extracts resume-worthy insights from code structure and git history. Produces structured, accuracy-annotated project summaries ready for resume generation.

## Adaptive Scanning Flow

### Step 0: Author Identity Confirmation

Before any analysis, establish the target author identity:

1. **state.json profile** — check `state.json.profile.git_authors[]` for previously confirmed identities
2. **--author param** — explicit author keys passed via CLI (`--author "wei, zhangyaowei"`)
3. **--detect-authors mode** — run `git shortlog -sne` and present top contributors for user selection
4. If no author can be determined, prompt the user interactively before proceeding
5. Store confirmed authors back to `state.json.profile.git_authors[]` for future runs

### Step 1: Quick Probe (All Projects)

Run a lightweight probe across every target path to collect surface-level signals:

- `ls` root directory structure (top-level layout, monorepo detection)
- README.md / README content (project purpose, badges, architecture diagrams)
- Dependency files: package.json, go.mod, Cargo.toml, requirements.txt, pom.xml, build.gradle
- Config files: Dockerfile, docker-compose, Makefile, CI configs (.github/workflows, .gitlab-ci)
- `Glob` file-count stats by extension (*.go, *.ts, *.py, etc.)
- AI signal files: CLAUDE.md, .cursorrules, .cursor/, Skills/, MCP configs, agent configs

Reference: [scan-strategy.md](references/scan-strategy.md) for the full quick-probe checklist.

### Step 2: Scale Judgment and Branching

After the quick probe, classify the project by size and branch strategy:

| Scale | File Count | Strategy |
|-------|-----------|----------|
| **Small** | < 50 files | Full scan — read every source file, complete understanding |
| **Medium** | 50–500 files | Key files — entry points, core modules, interfaces, tests |
| **Large** | > 500 files | Smart sampling — delegate to Subagents per top-level module |

For large projects, spawn parallel Subagents for independent module analysis, then merge results. Reference: [scan-strategy.md](references/scan-strategy.md) for key-file priorities and sampling rules.

### Step 3: Git Deep Analysis (When Author Available)

When a confirmed author identity exists, run deep git analysis:

1. **Contribution overview** — total commits, lines added/removed, files touched, active period
2. **Core module identification** — `git log --stat` by directory, find modules with highest author commit density
3. **Key contribution identification** — major features (large commits), refactors, new modules created from scratch
4. **Growth trajectory** — early commits vs recent commits, complexity progression
5. **Collaboration analysis** — co-authors, review patterns, PR interactions
6. **Before/after analysis** — diff from author's first commit parent to HEAD, split verifiable vs needs_confirmation
7. **Commit message analysis** — quality scoring, keyword mining, work pattern extraction

Reference: [git-analysis.md](references/git-analysis.md) for detailed git analysis rules.

### Step 4: Output

Write analysis results to the following locations:

- **Full analysis**: `~/resumes/projects/<project-name>/analysis.json`
- **Summary**: `state.json.projects[].summary` (concise project description)
- **Value points**: `state.json.projects[].value_points[]` (each with `data_source` annotation and optional `placeholders[]`)

All data points carry accuracy annotations per [accuracy-rules.md](references/accuracy-rules.md).

## Cache and Incremental Scanning

- Compute cache key from `git rev-parse HEAD` hash of the target repo
- Store in `state.json.projects[].git_head`
- On re-scan: compare current HEAD with stored `git_head` — skip if unchanged
- Use `--force` flag to bypass cache and re-scan regardless

## Command Syntax

```
wei-resume scan <path> [paths...]
  --author "key1, key2, key3"    # Author identifiers (name or email fragments)
  --since "2024-01-01"           # Only consider commits after this date
  --until "2025-06-01"           # Only consider commits before this date
  --detect-authors               # Interactive author detection from git shortlog
  --merge                        # Cross-project correlation analysis (tech evolution, common capabilities, role trajectory)
  --force                        # Bypass cache, force full re-scan
```

Multiple paths can be provided to scan several repositories in one invocation. Each path produces a separate project entry in state.json.

## Output Format

Each scanned project produces the following structure in `state.json.projects[]`:

```json
{
  "name": "my-project",
  "path": "/path/my-project",
  "scanned_at": "2026-04-18T10:00:00Z",
  "git_head": "abc123def",
  "scan_params": {
    "authors": ["zhangsan", "lisi"],
    "since": "2024-01-01",
    "until": null
  },
  "summary": {
    "type": "web-fullstack",
    "architecture": "前后端分离，Go 微服务 + React SPA",
    "tech_stack": ["Go", "TypeScript", "React", "gRPC", "K8s"],
    "author_period": "2024-03 ~ 2025-01",
    "author_ratio": "38%（团队 6 人）",
    "core_modules": ["services/order/（主负责人）", "pkg/middleware/（独立开发）"],
    "before_after": {
      "verifiable": ["模块数 3→8", "测试文件 0→47 个"],
      "needs_confirmation": ["测试覆盖率需用户确认具体数字"]
    },
    "code_quality": { "engineering": "高", "design": "清晰三层", "security": "JWT+RBAC" },
    "ai_signals": { "level": 3, "label": "AI Native", "details": ["CLAUDE.md", "Skills×2"] }
  },
  "value_points": [
    {
      "score": 95,
      "desc": "测试体系从零搭建（测试文件从 0 增至 47 个{placeholder:具体覆盖率}）",
      "category": "engineering",
      "data_source": "git_verifiable + context_required",
      "placeholders": [
        {
          "id": "test-coverage",
          "question": "测试覆盖率具体是多少？",
          "hint": "查看 CI 报告或运行 go test -cover，常见 60%-80%",
          "current_value": null
        }
      ]
    }
  ],
  "detail_path": "~/resumes/projects/my-project/analysis.json"
}
```

## Context Budget

| File | Estimated Lines |
|------|----------------|
| SKILL.md (this file) | ~100 |
| scan-strategy.md | ~100 |
| git-analysis.md | ~100 |
| accuracy-rules.md | ~50 |
| state.json (loaded portion) | ~150 |
| **Total** | **~500** |

Keep total loaded context under 500 lines to leave room for repository content during scanning.

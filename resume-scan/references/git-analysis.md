# Git Deep Analysis Reference

Rules and procedures for extracting resume-worthy insights from git history.

## 1. Contribution Overview

Run these commands to establish the author's overall footprint:

```bash
# Total commits by author
git log --author="<author>" --oneline | wc -l

# Lines added/removed
git log --author="<author>" --pretty=tformat: --numstat | awk '{add+=$1; del+=$2} END {print "added:", add, "removed:", del}'

# Files touched (unique)
git log --author="<author>" --pretty=tformat: --name-only | sort -u | wc -l

# Active period (first and last commit dates)
git log --author="<author>" --format="%ai" --reverse | head -1   # first
git log --author="<author>" --format="%ai" | head -1              # last
```

Record: total_commits, lines_added, lines_removed, files_touched, first_commit_date, last_commit_date. All are `git_verifiable`.

## 2. Core Module Identification

Identify which parts of the codebase the author owns:

```bash
# Commits per top-level directory
git log --author="<author>" --pretty=tformat: --name-only | sed 's|/.*||' | sort | uniq -c | sort -rn
```

- Rank directories by commit count from the author
- A "core module" is one where the author has 30%+ of all commits or is the top contributor
- Cross-reference with `git shortlog -sn -- <directory>` to get ownership percentage
- Mark module ownership as `git_estimated` (percentages depend on commit granularity)

## 3. Key Contribution Identification

Find the author's most significant work:

### Major Features (Large Commits)
```bash
# Largest commits by diff size
git log --author="<author>" --pretty=format:"%h %s" --shortstat | head -60
```
Look for commits that: add new directories, introduce new packages/modules, create 200+ lines in a single domain.

### Refactors
```bash
# Commits with high churn (many lines changed across many files)
git log --author="<author>" --pretty=format:"%h %s" --numstat | # filter for high file count + balanced add/remove
```
Refactors show equal adds and removes across many files. Look for keywords: refactor, restructure, reorganize, migrate.

### New Modules Created
```bash
# Files first introduced by author
git log --author="<author>" --diff-filter=A --pretty=format:"%h %ai" --name-only
```
Cluster new files by directory to identify modules the author created from scratch.

## 4. Growth Trajectory

Compare early vs late contributions to show professional growth:

- **First 20% of commits**: What kind of work? (bug fixes, small features, test additions)
- **Last 20% of commits**: What kind of work? (architecture decisions, large features, reviews)
- **Complexity progression**: File diversity increases, cross-module commits, infrastructure work
- Mark trajectory analysis as `git_estimated` — it involves interpretation

## 5. Collaboration Analysis

Extract teamwork signals:

```bash
# Co-authors from commit bodies
git log --author="<author>" --all --format="%b" | grep -i "co-authored-by" | sort | uniq -c | sort -rn

# Files also heavily edited by others (shared ownership)
# Compare author's file list with overall top contributors per file
```

- Identify frequent collaborators
- Detect mentoring signals: author introduces files that others later extend
- Review patterns: if PR/MR data is available, extract review frequency

## 6. Before/After Analysis

Quantify the author's total impact on the codebase:

```bash
# Find author's first commit
FIRST_COMMIT=$(git log --author="<author>" --reverse --format="%H" | head -1)
PARENT=$(git rev-parse ${FIRST_COMMIT}^)

# Full diff from before author joined to current state
git diff ${PARENT} HEAD --stat
git diff ${PARENT} HEAD --shortstat
```

### Split into verifiable vs needs_confirmation:

**Verifiable** (`git_verifiable`):
- Total files added/changed/deleted
- New directories/modules created
- Test file count increase
- New dependency additions
- New CI/CD pipeline files
- Lines of code delta

**Needs confirmation** (`context_required`):
- Performance improvement percentages
- Availability/reliability improvements
- User-facing impact metrics
- Business metric changes
- Team velocity improvements

Only use verifiable data directly. Generate placeholders for needs_confirmation items.

## 7. Commit Message Analysis

### Quality Scoring (0–10)
- **Conventionality** (0–3): Follows conventional commits? (feat:, fix:, refactor:, etc.)
- **Informativeness** (0–3): Describes *why* not just *what*? Includes context?
- **Granularity** (0–2): Atomic commits or kitchen-sink dumps?
- **Language quality** (0–2): Clear English, proper grammar, consistent style?

### Keyword Mining
Map commit prefixes/keywords to resume-relevant skills:

| Keyword Pattern | Resume Signal | Category |
|----------------|---------------|----------|
| feat, feature, add, implement | Feature delivery | Delivery |
| fix, bug, resolve, hotfix | Debugging ability | Problem-solving |
| refactor, restructure, clean | Code quality focus | Engineering maturity |
| perf, optimize, cache, speed | Performance tuning | Optimization |
| test, spec, coverage, e2e | Quality assurance | Testing |
| ci, cd, deploy, pipeline, docker | DevOps capability | Infrastructure |
| security, auth, vulnerability, cve | Security awareness | Security |
| docs, readme, comment, api-doc | Documentation discipline | Communication |
| migration, schema, database | Data engineering | Database |

### Keyword → Capability → Resume Mapping

| Keyword Pattern | Capability Signal | Resume Expression |
|----------------|-------------------|-------------------|
| feat / feature / add | 功能交付能力 | "独立设计并交付了 N 个核心功能" |
| fix / bugfix / hotfix | 问题排查能力 | "定位并修复 N 个核心缺陷" |
| refactor / restructure | 代码质量追求 | "主导了 XX 模块重构" |
| perf / optimize / performance | 性能优化意识 | "优化了 XX 性能" |
| test / coverage | 质量保障意识 | "建立了测试体系" |
| ci / cd / deploy / pipeline | DevOps 能力 | "搭建了 CI/CD 流水线" |
| security / auth / rbac | 安全意识 | "实现了认证授权体系" |
| migration / migrate | 数据治理经验 | "完成了数据迁移方案" |
| docs / readme / api-doc | 文档意识 | 加分项，不单独成 bullet |

### Work Pattern Extraction
```bash
# Commit hour distribution
git log --author="<author>" --format="%H %ai" | awk '{print $2}' | cut -d: -f1 | sort | uniq -c

# Day-of-week distribution
git log --author="<author>" --format="%ad" --date=format:"%u" | sort | uniq -c

# Commit frequency over time (monthly)
git log --author="<author>" --format="%ad" --date=format:"%Y-%m" | sort | uniq -c
```

- Time distribution: core hours vs off-hours patterns
- Commit granularity: average lines per commit, commits per day
- Weekend patterns: occasional vs regular weekend work (use carefully — can be sensitive)

## 8. Git Blame Sampling for Ownership

For core modules identified in step 2, sample files to classify ownership:

```bash
# For each key file, get blame stats
git blame --line-porcelain <file> | grep "^author " | sort | uniq -c | sort -rn
```

Classify each file the author touches:
- **Created**: Author wrote 80%+ of current lines and made the first commit
- **Heavily rewritten**: Author rewrote 50%+ of lines but didn't create the file
- **Maintained**: Author has 10–50% of lines, ongoing maintenance commits
- **Untouched**: Author has < 10% of lines (minor edits, not claimable)

Only claim "built" or "designed" for Created files. Use "improved" or "maintained" for others.

## 9. Squash Merge Detection

Large merge commits may hide individual contributions:

```bash
# Find merge commits with large diffs
git log --author="<author>" --merges --pretty=format:"%h %s" --shortstat
```

- Detect squash merges: single commit with 500+ line changes and merge-like message
- Flag these for manual review — the author may have done more granular work that got squashed
- Check if the repo uses squash-merge workflow (GitHub PR settings)

## 10. Co-Author Detection

```bash
# Search all commit bodies for co-authored-by
git log --all --format="%H%n%b" | grep -B1 -i "co-authored-by"
```

- Extract co-author names and emails
- Match against known team members if available
- Use for collaboration narrative: "partnered with X on Y"

## 11. Multi-Identity Merge

Detect and consolidate multiple git identities for the same person:

```bash
# All unique author identities
git shortlog -sne --all
```

- Flag similar names (case differences, name variants, typos)
- Flag similar email domains (personal vs work email)
- Present candidates to user for confirmation before merging
- Store confirmed identity mappings in `state.json.profile.git_authors[]`

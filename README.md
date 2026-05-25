# wei-resume — AI 驱动的代码转简历工具 | Code-to-Resume AI Toolkit

> **从代码仓库自动生成精准简历，用数据说话，拒绝编造。**
> Generate data-driven resumes from your codebase and Git history — no fabrication, every metric is sourced.

**Keywords**: 简历生成 / resume generator, 代码分析 / code analysis, Git 历史挖掘 / Git mining, 求职工具 / job search tool, AI 简历 / AI resume, Claude Code skill, 程序员简历 / developer resume, ATS 优化 / ATS optimization, 历史简历导入 / resume import, 简历优化 / resume enhancement

```
profile（我是谁）+ code（我做了什么）+ target（他要什么） → 精准简历
profile (who I am) + code (what I built) + target (what they want) → targeted resume
```

**一句话介绍 / TL;DR**: 扫描代码仓库自动提取量化成果，导入已有简历补充个人档案，结合目标岗位 JD 生成定向简历。所有数据可溯源，禁止 AI 编造。
Scan your codebase to extract achievements, import existing resumes to enrich your profile, and generate targeted resumes matched to job descriptions. Every metric is sourced — no AI fabrication.

## 快速开始

```bash
# 最快路径：零访谈，纯代码推断
wei-resume ./my-project --quick

# 有历史简历？导入后自动补全档案
wei-resume init --import resume.pdf          # 导入 PDF 简历，自动提取+诊断+追问
wei-resume init --import resume.docx         # 支持 PDF / DOCX / MD / TXT / HTML

# 完整流程
wei-resume init                    # 1. 建立个人档案（三轮访谈，可选导入历史简历）
wei-resume scan ./my-project       # 2. 扫描代码仓库
wei-resume target --jd "JD文本"    # 3. 设定目标岗位
wei-resume generate --target 字节  # 4. 生成定向简历

# 补充外部数据
wei-resume import --github <user>            # 导入 GitHub 画像（star、PR、贡献热力图）
wei-resume import --leetcode <user>          # 导入 LeetCode 排名和解题数据
wei-resume import --articles <url>           # 导入技术博客文章
```

## 核心特性

- **历史简历导入** — 支持 PDF / DOCX / MD / TXT / HTML，自动提取结构化信息，智能合并到个人档案，导入后自动诊断（量化度、具体性、成果导向、ATS 友好度）并针对性追问补充
- **外部数据补充** — 一键导入 GitHub 画像（star/PR/贡献热力图）、LeetCode 排名、技术博客、专利论文、Stack Overflow、掘金等，根据目标 JD 智能选择展示重点
- **代码智能分析** — 从 Git 历史、代码结构、Commit Message 中提取量化成果
- **数据准确性保障** — 所有数据标注来源（git_verifiable / code_inferable / user_confirmed），禁止编造
- **三阶段生成引擎** — 规划 → 逐段生成 → 组装校验，Section 级 context 隔离
- **多风格/多模板** — tech / hr / full 三种风格 × classic / modern / compact / academic 四套模板
- **迭代编辑** — 局部再生成 + 手动编辑保护，不覆盖用户修改
- **AI 能力展示** — 自动检测 AI 开发信号（CLAUDE.md、Skills、MCP），分级展示
- **ATS 优化** — 自动生成 ATS 友好版 + 关键词命中分析报告

## 技能包架构

```
wei-resume (路由入口)
├── resume-init          个人档案采集（三轮访谈 + 历史简历导入）
├── resume-scan          代码仓库智能扫描 + Git 深度分析
├── resume-target        目标岗位管理 + 匹配度分析
├── resume-generate      三阶段生成引擎
│   ├── Phase 1          规划 pass（结构+密度+叙事主线）
│   ├── Phase 2          逐 section 生成（plugin 机制）
│   └── Phase 3          组装 + 校验 pass
└── resume-import        外部数据导入（GitHub/博客/LeetCode/SO）
```

## 目录结构

```
path/
├── wei-resume/                    路由技能（入口）
│   ├── SKILL.md                   命令路由 + 公共规则 + schema 迁移
│   └── references/
│       └── state-schema.md        state.json 完整 schema
│
├── resume-init/                   个人档案采集
│   ├── SKILL.md
│   └── references/
│       ├── round1-basic.md        第一轮：确定性信息
│       ├── round2-explore.md      第二轮：发散性挖掘
│       ├── round3-deep.md         第三轮：人格+AI（合并）
│       └── import-strategy.md     历史简历导入策略
│
├── resume-scan/                   代码仓库扫描
│   ├── SKILL.md
│   └── references/
│       ├── scan-strategy.md       扫描策略 + 自适应规模判断
│       ├── git-analysis.md        Git 深度分析规则
│       └── accuracy-rules.md      数据准确性约束
│
├── resume-target/                 目标岗位管理
│   └── SKILL.md
│
├── resume-generate/               简历生成（三阶段引擎）
│   ├── SKILL.md
│   └── references/
│       ├── phase1-planning.md     规划规则
│       ├── phase3-assembly.md     组装校验规则
│       ├── rules-accuracy.md      数据准确性规则
│       ├── rules-ats.md           ATS 优化规则
│       ├── rules-power-verbs.md   措辞优化规则
│       ├── sections/              Section Plugins（8 个）
│       │   ├── header.md
│       │   ├── summary.md
│       │   ├── skills.md
│       │   ├── experience.md
│       │   ├── projects.md
│       │   ├── education.md
│       │   ├── ai-capability.md
│       │   └── extras.md
│       ├── templates/             模板（4 套）
│       │   ├── classic/           简洁单栏，适合正式投递
│       │   ├── modern/            现代双栏，适合互联网
│       │   ├── compact/           极致一页，适合海投
│       │   └── academic/          学术风格，强调论文
│       └── industries/            行业差异化规则（6 个）
│           ├── fintech.md
│           ├── gaming.md
│           ├── ai.md
│           ├── infra.md
│           ├── startup.md
│           └── remote.md
│
└── resume-import/                 外部数据导入
    └── SKILL.md
```

## 命令参考

### 主命令

```bash
wei-resume <path> [paths...]       # 快捷方式：扫描 + 生成
  --author "key1, key2"            # Git 作者匹配
  --target "岗位名"                # 定向生成
  --style tech|hr|full|all         # 风格（默认 all）
  --template classic|modern|compact|academic
  --industry fintech|gaming|ai|infra|startup|remote
  --lang zh|en|both                # 语言（默认 zh）
  --format md|html|pdf|all         # 格式（默认 md+html）
  --pages 1|2|auto                 # 页数（默认 auto）
  --ats                            # 同时生成 ATS 版
  --quick                          # 零访谈快速模式
  -i                               # 交互模式
```

### 子命令

| 命令 | 说明 |
|------|------|
| `wei-resume init` | 三轮访谈建档 |
| `wei-resume init --import <file>` | 导入历史简历（PDF/DOCX/MD/TXT/HTML），自动提取+诊断+追问 |
| `wei-resume init --update` | 增量更新已有档案 |
| `wei-resume scan <path>` | 扫描代码仓库 |
| `wei-resume target --jd "..."` | 添加目标岗位 |
| `wei-resume import --github <user>` | 导入 GitHub 画像（star/PR/贡献热力图） |
| `wei-resume import --github <user> --depth full` | 深度分析 GitHub（PR/Issue 详情） |
| `wei-resume import --leetcode <user>` | 导入 LeetCode 排名和解题数据 |
| `wei-resume import --stackoverflow <user-id>` | 导入 Stack Overflow 声望和回答 |
| `wei-resume import --juejin <user>` | 导入掘金画像 |
| `wei-resume import --articles <url>` | 导入技术博客主页（全部文章） |
| `wei-resume import --article <url>` | 导入单篇文章 |
| `wei-resume import --patent <number>` | 导入专利 |
| `wei-resume import --paper <title>` | 导入学术论文 |
| `wei-resume import --feedback <file>` | 导入绩效评价 / 同事评价 |
| `wei-resume generate` | 生成简历 |
| `wei-resume edit --section projects` | 局部再生成 |
| `wei-resume status` | 查看当前状态 |

## 三种工作模式

| 模式 | 触发 | 适用场景 |
|------|------|---------|
| 零访谈快速 | `--quick` 或首次无档案 | 快速看效果，后续补充 |
| 快速 | 有 state.json，无 `-i` | 日常使用，一键生成 |
| 交互 | `-i` | 精细控制每个 section |

## 数据准确性

所有量化数据必须标注来源，禁止 AI 编造：

| 来源类型 | 可信度 | 示例 |
|----------|--------|------|
| git_verifiable | 高 | commit 数、文件数、代码行数 |
| code_inferable | 高 | 技术栈、架构模式 |
| user_confirmed | 高 | 用户确认的性能数据 |
| context_required | 待确认 | QPS、延迟（用占位符标记） |

**严禁编造**：性能指标、可用性指标、业务指标、具体百分比提升。

## 存储

所有产出存放在 `~/resumes/`：

```
~/resumes/
├── state.json              唯一真相源
├── projects/               项目分析详情
├── targets/                岗位 JD 详情
├── github/                 GitHub 画像缓存
├── output/                 生成的简历
└── versions/               版本快照
```

## 依赖

- Claude Code 内置工具（Glob / Grep / Read / Write / Bash）
- chrome-devtools MCP（抓取招聘链接）
- GitHub MCP（可选，GitHub 画像分析）
- 无外部脚本依赖

## 设计文档

- [V3 设计方案](docs/plans/2026-04-19-code2resume-design-v3.md)

---
name: wei-resume
description: Use when user wants to generate resumes from code repositories, analyze code for resume content, manage job targets, or any resume-related task. Triggers include "wei-resume", "代码转简历", "简历生成", "resume from code", "分析代码写简历".
---

# wei-resume — 代码转简历路由技能

## 核心理念

```
profile（我是谁）+ code（我做了什么）+ target（他要什么） → 精准简历
```

从源码和 git 历史中自动提取项目经验，结合个人档案和目标岗位 JD，生成有数据支撑的定向简历。

## 命令路由表

解析用户输入，匹配第一个命中的规则，用 Read 工具加载对应 SKILL.md 并按其指令执行。

| 用户输入 | 路由目标 |
|---------|---------|
| `wei-resume init [...]` | Read `resume-init/SKILL.md` → 执行 |
| `wei-resume scan <path> [...]` | Read `resume-scan/SKILL.md` → 执行 |
| `wei-resume target [...]` | Read `resume-target/SKILL.md` → 执行 |
| `wei-resume import [...]` | Read `resume-import/SKILL.md` → 执行 |
| `wei-resume generate [...]` | Read `resume-generate/SKILL.md` → 执行 |
| `wei-resume edit [...]` | Read `resume-generate/SKILL.md`（edit mode）→ 执行 |
| `wei-resume <path> [...]` | Read `resume-scan/SKILL.md` → 扫描；然后 Read `resume-generate/SKILL.md` → 生成 |
| `wei-resume status` | 直接处理：读 state.json，展示档案/项目/岗位/产出概览 |
| `wei-resume config [...]` | 直接处理：读写 state.json.config |
| `wei-resume history [...]` | 直接处理：读 state.json.outputs + ~/resumes/versions/ |
| `wei-resume privacy-check` | 直接处理：扫描 state.json + ~/resumes/ 中的敏感信息 |

路由机制：匹配子命令后，用 Read 工具加载 `/Users/wei/Code/Wei-Skills/code2resume/<子技能>/SKILL.md`，然后严格按该文件中的指令执行。快捷方式 `wei-resume <path>` 依次加载 scan 和 generate。

## 三种模式

| 模式 | 触发条件 | 行为 |
|------|---------|------|
| 零访谈快速模式 | `--quick` 或无 state.json | 纯代码推断，不问任何问题，生成 80 分简历 |
| 快速模式 | 有 state.json，无 `-i` | 自动 scan → analyze → generate，使用默认配置 |
| 交互模式 | `-i` / `--interactive` | 分步确认：扫描→占位符补全→配置→逐 section 审核 |

零访谈模式下生成后提示：运行 `wei-resume init` 补充个人信息可显著提升质量。

## Schema 迁移

读取 state.json 时执行以下迁移逻辑：

```
1. 读取 _meta.schema_version（缺失视为 1）
2. 若 version < 3，按顺序执行迁移：
   v1→v2:
     - profile.target → profile.target_direction（重命名）
     - 新增 outputs[] / config / _meta 字段（空默认值）
   v2→v3:
     - projects[].summary.before_after: string[] → {verifiable, needs_confirmation}
     - value_points[] 新增 data_source / placeholders 字段
     - outputs[] 新增 edits / plan_path / sections_path 字段
     - _meta 新增 migrations_applied[]
3. 更新 _meta.schema_version = 3
4. 追加 _meta.migrations_applied: ["vN→vM at <timestamp>"]
5. 写回 state.json
```

## 全局规则

1. **输出目录**：所有产出写入 `~/resumes/`，state.json 是唯一真相源
2. **隐私**：永远不要将敏感数据（手机号、真实邮箱）提交到 git；`--redact` 模式下自动脱敏
3. **数据准确性**：所有量化数据必须标注来源（git_verifiable / code_inferable / user_confirmed）；禁止编造性能指标、业务指标、具体百分比提升
4. **占位符**：无法确认的数据用 `{placeholder:描述}` 标记，交互模式下引导用户填充
5. **Context 控制**：每个子技能独立运行，只加载自身 SKILL.md + 所需 references；state.json 是跨技能唯一桥梁

## State Schema

完整 schema 定义见 `wei-resume/references/state-schema.md`。

读写契约概要：

| 技能 | 读 | 写 |
|------|---|---|
| resume-init | _meta | profile.* |
| resume-scan | profile.git_authors | projects[] |
| resume-target | profile + projects | targets[] |
| resume-import | profile | profile.* / projects[] |
| resume-generate | profile + projects + targets + config | outputs[] |
| wei-resume (路由) | _meta (迁移) | _meta |

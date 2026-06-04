# Section Plugin: Experience（工作经历）— Overview Layer

## 定位

Experience 是**概览层**，回答"你在这家公司的角色和整体贡献是什么"。
具体技术方案、实现细节属于 Projects section，Experience 不涉及。

## 输入数据
- resume-plan.json 中的 sections[id=experience] 配置（max_bullets_per_job, style_notes）
- state.json.profile.career[]（公司、职位、时间、highlights）
- target.keywords（如有，用于措辞对齐）

## 生成规则

1. 倒序排列（最近的工作在前）
2. 每个工作条目格式：
   ```
   ### 公司名 | 职位 | 时间段
   一句话定位（参与了什么业务线/团队）
   - 高层概括 bullet 1（角色 + 核心贡献）
   - 高层概括 bullet 2（关键成果/难题攻克）
   - 高层概括 bullet 3（持续交付/团队认可）
   ```
3. **一句话定位**（bullets 之前）：简要说明在该公司参与了哪些业务线或团队。
   - 示例："参与小米汽车 DCC 和国际零售 POS 两个业务线。"
   - 示例："隶属基础架构团队，支撑全公司前端工程体系。"
4. Bullet 数量：遵守 max_bullets_per_job（默认 2-3 条）
5. 每条 bullet 以 Power Verb 开头（参考 rules-power-verbs.md）
6. **Bullet 内容定位 — 高层概括，非技术深潜**：
   - 聚焦：角色范围、团队贡献、关键成果、获得认可
   - 不写：架构细节、具体算法、代码层信息
   - 可提及量化指标（commit 数、模块数）但不展开实现细节
   - 即使 tech 风格，也使用偏业务的语言
7. Bullet 内容来源：
   - career[].highlights（用户在 init 时提供的）
   - 如果 highlights 为空但有关联 project → 从 project 中提取高层概括
   - 如果都没有 → 基于职位推断通用描述（标注为 context_required）
8. 当前工作用现在时，过去工作用过去时

## 与 Projects 的去重

Experience 与 Projects 各有分工，内容**不重叠**：
- Experience 说**做了什么**："主导 X 系统重构"
- Projects 说**怎么做的**：架构选型、实现方案、具体指标

去重规则：
- Phase 2 生成 experience 时，读取 resume-plan.json 中 projects section 已分配的 value_points
- Experience bullet 只保留高层概括，将技术实现细节留给 Projects
- 如果某条 highlight 的细节已被 projects 引用 → experience 仅保留一句概括
- 如果概括后 bullets 不足 max_bullets_per_job → 从 career highlights 中补充下一条

## 风格变体

### Tech 风格
- 可在高层提及技术栈名称，但不展开实现
- 聚焦：用了什么技术做了什么事
- 示例："基于 Vue 3 + WebSocket 主导呼叫中心系统重构，交付 5 个核心模块"

### HR 风格
- 聚焦：团队角色、领导力、获得认可
- 使用纯业务语言
- 示例："成为 15 人前端团队最大贡献者，获技术负责人认可并承担核心模块 owner 职责"

### Full 风格
- 平衡技术提及与团队角色
- 示例："主导呼叫中心系统重构，成为 15 人团队最大贡献者，独立交付 5 个核心业务模块"

## 时间格式
- zh: 2021.03 - 2024.06 / 2024.06 - 至今
- en: Mar 2021 - Jun 2024 / Jun 2024 - Present
- 由 --lang 参数决定

## context_required 标记
- 当某条 bullet 是基于职位推断而非用户提供的实际数据时
- 在生成的 Markdown 中以 HTML 注释标注：`<!-- context_required: 需要用户确认 -->`
- 后续 review 阶段会提示用户确认或替换

## Trim Priority
1. Reduce bullets per job (3→2→1)
2. Remove oldest job entry if 3+ jobs listed
3. Never remove current/most recent job

## 输出格式
纯 Markdown，写入 sections/experience.md

## Kami 模板输出格式

当 `--template kami` 时，experience 的职责发生变化：工作经历的高层概括由 header 插件的 `<!-- KAMI:TIMELINE -->` 承担（3 步职业弧线），experience 插件**不再重复输出**。

experience 插件在 kami 模式下仅负责：
1. 提供 `{{EXPERIENCE_DATE_RANGE}}` 值（如 "2019 - 至今（8 年）"）
2. 确认时间线 3 步的年份和标题（与 header 的 timeline 一致）

```markdown
<!-- KAMI:EXPERIENCE_META -->
**日期范围：** 2019 - 至今（8 年）
<!-- /KAMI:EXPERIENCE_META -->
```

时间线内容（3 步弧线）由 header.md 的 `<!-- KAMI:TIMELINE -->` 块输出，experience 插件不需要重复生成。

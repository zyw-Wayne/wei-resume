# Section Plugin: Summary（个人概述）

## 输入数据
- resume-plan.json 中的 sections[id=summary] 配置（含 style_notes, max_lines, narrative_theme）
- state.json.profile.persona (tagline, career_narrative, work_style)
- state.json.profile.strengths (tech_direction, team_role, highlights)
- state.json.profile.ai（如有 level >= 3 则提及）
- 最亮眼的 social_proof（如有，且数据非常突出时嵌入）

## 生成规则
1. 长度：严格遵守 max_lines（通常 3-4 行）
2. 开头用 persona.tagline 改写（不要原样照抄，根据 target 调整措辞）
3. 中间用 1-2 句话展示核心竞争力（基于 highlights + tech_direction）
4. 结尾指向目标方向（如有 target → 对齐 JD 关键词）
5. 避免空泛的自我评价（"热爱学习"、"团队精神"）→ 用具体证据替代

## 风格变体

### Tech 风格
- 强调技术深度：具体技术栈 + 架构能力 + 量化成果
- 示例："8 年后端开发经验，专注 Go 微服务架构，主导过日均 50 万订单系统从单体到微服务的演进"

### HR 风格
- 强调团队贡献和业务价值
- 示例："从业务开发成长为技术负责人，带领 6 人团队交付核心交易系统，注重工程质量和团队成长"

### Full 风格
- 两者兼顾，技术+业务+人
- 示例："8 年后端工程师，从业务开发成长为架构负责人。专注 Go/K8s 技术栈，主导订单系统微服务化，带领 6 人团队保障核心链路稳定性"

## narrative_theme 处理
- growth: 强调成长轨迹（从 X 到 Y）
- expertise: 强调专业深度（N 年深耕 X 领域）
- impact: 强调业务影响力（量化成果优先）
- leadership: 强调带队和技术影响力

## AI 能力嵌入规则
- ai.level >= 3 → 自然嵌入一句（如"积极将 AI 工具融入研发流程，提效 30%"）
- ai.level < 3 → 不提及（避免弱经验反而减分）
- 嵌入位置在核心竞争力之后、目标方向之前

## Trim Priority
Reduce from 4 lines to 2 lines. Never remove entirely.

## 输出格式
纯 Markdown 段落（无标题），写入 sections/summary.md

## 注意事项
- 不要以"我"开头（中文简历惯例）
- social_proof 只在数据特别突出时嵌入（如 GitHub 1k+ stars），否则留给 projects section
- 如果 max_lines 为 2，则只保留 tagline 改写 + 一句核心竞争力

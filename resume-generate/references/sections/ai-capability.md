# Section Plugin: AI Capability（AI 能力展示）

## 输入数据
- resume-plan.json 中的 sections[id=ai-capability] 配置（condition, max_lines）
- state.json.profile.ai (tools, skills, cognition, workflow)
- state.json.projects[].summary.ai_signals[]

## 前置条件
此 section 仅在 AI level >= 2 时生成（由 Phase 1 plan 中 condition 控制）

## AI Level 判定
- Level 0: 无 AI 使用记录 → 不展示此 section
- Level 1: 只用 Copilot/ChatGPT 辅助 → skills 栏加一行"AI 辅助开发"，不独立 section
- Level 2: 有 Prompt/Context Engineering 实践 → 独立 section，2-3 条
- Level 3: 有 Agent/Skills/MCP 开发经验 → 独立 section + 项目中重点展示
- Level 4: 有公开输出（博客/开源/演讲） → 独立 section + extras 中展示影响力

## 生成规则
1. 展示维度（按 level 递增选择）：
   - AI 工具链：日常使用的工具 + 使用占比
   - AI 开发范式：Vibe Coding / Context Engineering / Agent Development
   - AI 工程实践：Skills 开发、MCP Server 搭建、Prompt 工程
   - AI 项目成果：具体的 AI 相关项目/产出
   - AI 影响力：文章、开源、演讲
2. 每条 bullet 要有具体证据（来自 projects[].ai_signals 或 profile.ai.skills）
3. 避免空泛描述（❌ "熟悉 AI 工具" → ✅ "基于 Claude Code + Custom Skills 构建自动化开发工作流，编码效率提升 60%"）
4. 如有 profile.ai.workflow → 展示为工作流描述
5. 如有 profile.ai.cognition.core_view → 可作为收尾观点（仅 full 风格）

## Trim Priority
1. Reduce to 2 bullets maximum
2. Remove workflow description
3. Remove entire section (merge one highlight into skills section instead)

## 输出格式
纯 Markdown，写入 sections/ai-capability.md

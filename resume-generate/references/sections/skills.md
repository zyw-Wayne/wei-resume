# Section Plugin: Skills（技能栏）

## 输入数据
- resume-plan.json 中的 sections[id=skills] 配置（sort_strategy, max_items, grouping）
- state.json.projects[].summary.tech_stack（所有项目的技术栈汇总）
- state.json.profile.ai（AI 工具和技能）
- target.tech_required + tech_preferred（如有 target）

## 生成规则
1. 排序策略：
   - target_match_first: target required → target preferred → 按使用频率
   - frequency_first: 按跨项目出现频率排序
2. 分组（由 plan.grouping 指定，通常 3 组）：
   - 核心技术（编程语言 + 框架）
   - 基础设施（数据库 + 中间件 + 云 + DevOps）
   - AI 工程（AI 工具 + 范式，仅 ai_level >= 2 时出现）
3. 展示方式由模板决定：
   - inline-list: "Go / TypeScript / React / gRPC / K8s / PostgreSQL"
   - grouped-badges: 分组展示，每组一行
   - comma-separated: 紧凑逗号分隔
   - categorized-list: 分类详细列表
4. 最大项数：遵守 max_items（通常 12-15 个）
5. 不列没用过的技术（必须在 projects 的 tech_stack 中出现过）
6. 技术名称标准化：用官方拼写（TypeScript 不是 Typescript，Kubernetes 不是 k8s，除非空间极紧）

## 分层规则（仅 categorized-list 展示方式使用）
- 精通/Expert: 跨 2+ 项目使用且为核心技术
- 熟练/Proficient: 单项目核心使用
- 了解/Familiar: 辅助使用

## 频率计算
- 遍历所有 state.json.projects[].summary.tech_stack
- 统计每项技术出现的项目数量
- 同一项目内重复出现只计一次

## AI 工程组内容
- 工具：GitHub Copilot, Claude, ChatGPT, Cursor 等
- 范式：Prompt Engineering, RAG, Agent, Fine-tuning 等
- 仅列出实际在项目中应用过的（不列"体验过"级别的）

## target 匹配逻辑
- target.tech_required 中的技术如果在 tech_stack 中存在 → 必选，排最前
- target.tech_preferred 中的技术如果在 tech_stack 中存在 → 优先选入
- 未匹配的 required 技术不要伪造（不在 tech_stack 中就不列）

## Trim Priority
1. Remove "了解/Familiar" tier items first
2. Reduce max_items by 30%
3. Collapse grouping to single flat list

## 输出格式
纯 Markdown，写入 sections/skills.md

## 示例输出（grouped-badges）
```markdown
**核心技术：** Go / TypeScript / React / Node.js / gRPC
**基础设施：** PostgreSQL / Redis / Kubernetes / Docker / AWS
**AI 工程：** GitHub Copilot / Prompt Engineering / RAG
```

## Kami 模板输出格式

当 `--template kami` 时，skills 输出 5 行「标签 + 具体示例」结构（非技术栈列表）。每行回答「你会什么 + 在哪用过」：

```markdown
<!-- KAMI:SKILLS -->
1. **系统架构** · 从零设计微服务架构，服务拆分 + API 网关 + 服务发现，支撑 3 个核心业务系统
2. **性能优化** · 主导前端性能治理，首屏加载从 4.2s 降至 1.1s，LCP P95 稳定在 1.5s 以内
3. **AI 工程** · 构建 RAG + Agent 混合架构，集成 Claude API，实现智能客服自动应答率 65%
4. **团队管理** · 带领 8 人团队，建立 Code Review + 技术分享机制，2 名成员在 12 个月内晋升
5. **工程效能** · 搭建 CI/CD 流水线 + 自动化测试体系，部署频率从周级提升至日级
<!-- /KAMI:SKILLS -->
```

**Kami skills 规则：**
- 5 行固定，不多不少
- 每行格式：`标签 · 具体示例（含数字）`
- 标签 ≤4 个字（CN）/ ≤2 个词（EN），用 `<span class="em-brand">` 高亮示例中的关键能力词
- 示例必须包含至少一个可量化指标或具体成果
- 不列技术栈列表（那是其他模板的 skills 展示方式）

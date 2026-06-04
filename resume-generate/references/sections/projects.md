# Section Plugin: Projects（项目经历）— Main Content Layer

## 定位

Projects 是简历的**核心内容层**，回答"你具体做了什么、为什么做、怎么做的、效果如何"。
每个项目以业务导向的深度描述呈现，结构清晰、层次分明。

## 输入数据
- resume-plan.json 中的 sections[id=projects] 配置（selected_projects, selected_value_points, max_bullets_per_project, style_notes）
- state.json.projects[].summary（按 plan 中 selected_projects 筛选）
- state.json.projects[].value_points（按 plan 中 selected_value_points 筛选）
- ~/resumes/projects/*/context.md（用户补充的项目上下文，如有）
- target.keywords（如有，用于措辞对齐）

## 生成规则

1. 每个项目格式：
   ```
   ### 项目名称（公司/来源）

   **技术栈：** Vue 3 / TypeScript / WebSocket / Electron
   **角色：** 核心开发 / 技术负责人

   背景段落（2-3 句）：业务场景 + 你的角色定位 + 核心贡献概述。
   告诉读者这个项目是干什么的、为什么重要、你在其中扮演什么角色。

   - 模块/能力 1（高层描述）
     - 具体实现细节 1
     - 具体实现细节 2
   - 模块/能力 2（高层描述）
     - 具体实现细节 1
     - 具体实现细节 2
   - 模块/能力 3（高层描述）
     - 具体实现细节 1
   ```

2. **项目排序**：按 Phase 1 选择算法的 selection score 降序（最高分项目在前）

3. **背景段落**（2-3 句，bullets 之前）：
   - 第 1 句：这个项目是什么、服务谁
   - 第 2 句：你的角色、工作范围
   - 第 3 句（可选）：关键成就或独特挑战
   - 风格变体影响段落侧重：
     - tech: "XXX 系统的核心前端模块，采用 YYY 架构..."
     - hr: "服务 ZZZ 用户的核心业务系统，独立负责..."
     - full: "XXX 业务系统的核心前端，独立主导 YYY，服务 ZZZ..."

4. **层级式 Bullet 规则**：
   - **顶层 bullet**：描述一个**模块**或**能力**（如"呼叫中心集成"、"AI 智能辅助体系"）
   - **子 bullet**：该模块下的具体实现细节
   - 通常每个项目 2-4 个顶层 bullet，每个顶层下 2-3 个子 bullet
   - 顶层和子 bullet 均遵循 Power Verb + 做了什么 + 结果 的模式

5. 技术栈行：target_keywords 命中的排前 + Top 3 未命中的

6. **个人项目**：每个单独列出，使用相同结构（背景段落 + 层级 bullets），不合并为一个条目

7. **其他项目**（所有选中项目之后）：
   ```
   **其他项目：** OBJ 模型批处理桌面工具（Electron）· 24h 留尿记录 H5 应用 · 50+ 国家角色配置脚本
   ```
   未被选中但值得一提的项目，以一行 · 分隔的形式列出。

8. **数据准确性**（最重要）：
   - data_source = git_verifiable → 可直接使用具体数字
   - data_source = code_inferable → 可描述但不编造数字
   - data_source = context_required + placeholder 已填 → 使用用户确认的数字
   - data_source = context_required + placeholder 未填 → 用模糊表述
   - 示例：✅ "从零搭建测试体系（新增 47 个测试文件）" ❌ "测试覆盖率达到 80%"

9. **角色标注**：基于 git 分析中的所有权分类
   - 创建者 → "从零设计并实现" / "独立开发"
   - 大幅重写 → "重构了" / "重新设计了"
   - 核心维护 → "持续优化" / "负责维护"

## 风格变体

### Tech 风格
- 背景段落侧重技术架构
- 子 bullet 包含具体参数、算法、协议等技术细节
- 结构：动词 + 方案 + 技术细节 + 结果数据
- 示例顶层："设计呼叫中心集成模块"
  - 子 bullet："基于 WebSocket 长连接实现坐席状态实时同步，心跳间隔 30s，断线自动重连策略支撑 99.9% 在线率"

### HR 风格
- 背景段落侧重业务价值与用户规模
- 子 bullet 聚焦用户影响、业务价值、交付成果
- 避免过深技术细节，用结果说话
- 示例顶层："主导呼叫中心业务模块"
  - 子 bullet："支撑 200+ 坐席日常外呼与接听，系统上线后客服响应效率提升 35%"

### Full 风格
- 背景段落平衡技术与业务
- 子 bullet 兼顾技术实现与业务上下文
- 非专业读者也能理解 80%
- 示例顶层："设计并交付呼叫中心集成模块"
  - 子 bullet："基于 WebSocket 实现坐席状态实时同步，支撑 200+ 坐席日常运营，客服响应效率提升 35%"

## Trim Priority
1. 移除最低分项目的子 bullet（保留顶层 bullet）
2. 移除每个项目第 4 个及之后的顶层 bullet
3. 移除"其他项目"一行
4. 整体移除最低分项目
5. 缩减至最多 4 个项目

## Kami 模板输出格式

当 `--template kami` 时，项目输出格式切换为**三段式表格**，适配 kami 的 `.proj-lines` 结构。Phase 3 从 markdown 中提取三段内容填入模板。

### 输出结构

每个项目输出三个带标记的段落（用 HTML 注释标记，方便 Phase 3 提取）：

```
### 项目名称
**类型：** 核心平台 · **角色：** 方向主导

<!-- KAMI:ROLE -->
~60 字：项目是什么 + 为什么做 + 你的位置。不使用动词开头，用名词性描述。
<!-- /KAMI:ROLE -->

<!-- KAMI:ACTIONS -->
~80 字：技术方案 / 关键决策 / 执行路径。动宾结构开头，一个具体方案一句。
<!-- /KAMI:ACTIONS -->

<!-- KAMI:IMPACT -->
~100 字：数据为王。用 `<span class="hl">关键数字</span>` 高亮 1-2 处核心指标。
<!-- /KAMI:IMPACT -->
```

### 三段式规则

| 段落 | 字数限制 (CN) | 字数限制 (EN) | 内容要求 |
|------|-------------|-------------|---------|
| 角色 / Role | max 60 字 | max 40 词 | 项目是什么 + 为什么做 + 你的位置。名词性描述，不用动词开头 |
| 动作 / Actions | max 80 字 | max 55 词 | 技术决策 + 执行路径。遵循 rules-power-verbs.md 动词表，动宾结构开头 |
| 结果 / Impact | max 100 字 | max 65 词 | 量化结果。必须有 1-2 个 `<span class="hl">` 高亮数字 |

**数据准确性**：遵循 rules-accuracy.md。Impact 段的高亮数字必须有 git_verifiable / user_confirmed 来源，不可编造性能指标、业务指标、具体百分比提升。

### 与标准格式的映射

| 标准格式 (层级 bullets) | Kami 三段式 |
|------------------------|------------|
| 背景段落 (2-3 句) | → 角色段 |
| 顶层 bullet 1-2 (模块/能力) | → 动作段 |
| 子 bullet (实现细节) | → 动作段（精简合并） |
| 最后一个顶层 bullet (成果) | → 结果段 |

### 其他项目

Kami 模板下，其他项目以一行列出，放在项目区块末尾：
```
**其他项目：** 项目A（技术栈）· 项目B（技术栈）· 项目C（技术栈）
```

### Dense 变体

当 Phase 1 选择了 5+ 个项目时，Phase 3 在填入 kami 模板时自动添加 `class="resume--dense"` 到 `<body>`。6 个项目需要进一步压缩（行高 1.36/1.38），应视为信号：优先编辑项目列表而非压缩排版。
6. 永远不移除最高分项目

## 输出格式
纯 Markdown，写入 sections/projects.md

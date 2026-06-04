# Section Plugin: Header（头部信息）

## 输入数据
- resume-plan.json 中的 sections[id=header] 配置
- state.json.profile.basic (name_zh, name_en, phone, email, links)

## 生成规则
1. 按模板 header_style 决定布局：centered / left-aligned-with-color-bar / single-line / academic-centered
2. 姓名格式由 --lang 决定：zh→中文名, en→English Name, both→两者
3. 联系方式排列：phone | email | GitHub | Blog | LinkedIn（用竖线分隔）
4. 链接只展示域名+用户名，不展示完整 URL（除非单行放不下）
5. 如果 links 中有 GitHub 且 profile.strengths.open_source 非空 → 加粗 GitHub 链接
6. --redact 模式：phone 显示前3后4打星，email 显示首字母+@+域名

## 布局说明

### centered
姓名居中，联系方式居中排列在姓名下方一行。适合学术 / 简洁风格。

### left-aligned-with-color-bar
姓名左对齐，联系方式同行右对齐。顶部带品牌色条（由模板 CSS 控制）。

### single-line
姓名与联系方式在同一行，紧凑排列。适合一页纸简历。

### academic-centered
姓名居中加粗，联系方式分两行居中（第一行 phone | email，第二行 links）。

## Trim Priority
This section cannot be trimmed. Always present in full.

## 输出格式
纯 Markdown，写入 sections/header.md。示例：

```markdown
# 张耀伟 | Yaowei Zhang
138****1234 | w**@example.com | github.com/wei | blog.wei.dev
```

## 注意事项
- 不生成任何 section 标题（如"个人信息"），header 本身就是简历的起始区域
- 如果 name_en 缺失且 --lang=both，则只显示中文名（不要编造英文名）
- links 顺序：GitHub > Blog > LinkedIn > 其他（按职业相关性排序）

## Kami 模板输出格式

当 `--template kami` 时，header 输出使用结构化标记，方便 Phase 3 提取填入模板：

```markdown
<!-- KAMI:HEADER -->
**姓名：** 张耀伟
**别名：** Yaowei Zhang
**岗位定位：** AI / Agent 工程
**GitHub：** https://github.com/wei (wei)
**X/Twitter：** https://x.com/wei (wei)
**电话：** 138****1234
**邮箱：** w**@example.com
**城市：** 北京
**年龄：** 28
<!-- /KAMI:HEADER -->

<!-- KAMI:SUMMARY -->
现任 XX 团队技术负责人，主导 YY 系统从 0 到 1 建设。团队 8 人，覆盖前后端 + AI。核心沉淀：Agent 架构、性能优化、工程效能。
<!-- /KAMI:SUMMARY -->

<!-- KAMI:METRICS -->
- **8** 年 · 软件开发经验
- **50** 人 · 跨团队协作规模
- **3** 个 · 从零到一的核心系统
- **40%** · 最大性能提升幅度
<!-- /KAMI:METRICS -->

<!-- KAMI:TIMELINE -->
- **2019** · 基础建设期：从全栈开发切入，建立工程规范和 CI/CD 体系
- **2022** · 转型突破期：转向 AI 工程，主导首个 Agent 系统落地
- **2024** · 体系化输出：形成 Agent + 性能双轮驱动的技术方向
<!-- /KAMI:TIMELINE -->
```

**Metrics 选择规则**（4 个）：1 个时间维度 + 1 个规模维度 + 2 个结果维度。
**Timeline 规则**：3 步职业判断弧线（非工作经历罗列）—— 基础期 / 转折期 / 当前期。

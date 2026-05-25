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

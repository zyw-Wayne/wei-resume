# Section Plugin: Extras（附加信息）

## 输入数据
- resume-plan.json 中的 sections[id=extras] 配置（max_lines, data_keys）
- state.json.profile.strengths (certifications, patents, open_source, articles, talks)
- state.json.profile.social_proof[]
- resume-plan.json.narrative_theme（决定呈现优先级）
- target（如有，影响选择策略）

## 生成规则
1. 从以下数据池中选择最有价值的内容（遵守 max_lines，通常 2-3 行）：
   - 证书 (certifications)
   - 专利 (patents)
   - 开源项目 (open_source)
   - 技术文章 (articles)
   - 演讲/分享 (talks)
   - 社交平台数据 (social_proof)
2. 选择策略（有 target 时）：
   - JD 强调算法 → LeetCode 数据优先："LeetCode 342 题，Rating 1856（Top 8%）"
   - JD 强调社区影响力 → SO + 掘金："Stack Overflow Go 标签 2340 声望"
   - JD 强调开源 → GitHub："GitHub 活跃贡献者，项目 XXX 120+ Star"
   - 无特别倾向 → 选最亮眼的 1-2 项
3. 选择策略（无 target 时）：
   - 有排名数据（Top X%）> 有绝对数字 > 只有活跃度
4. 呈现格式：简洁的列表，每项一行
5. 不展示不够亮眼的数据（宁可少写不要凑数）
6. 如有多个证书/专利 → 只列最相关的，末尾加"等 N 项"

## Trim Priority
First section to be trimmed entirely. Only survives if page budget allows.
Remove in order: average-tier items → notable-tier items → entire section.

## 输出格式
纯 Markdown 列表，写入 sections/extras.md

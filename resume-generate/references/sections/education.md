# Section Plugin: Education（教育背景）

## 输入数据
- resume-plan.json 中的 sections[id=education] 配置（max_lines）
- state.json.profile.education[]

## 生成规则
1. 格式：`学校名称 | 专业 | 学位 | 时间段`
2. 按时间倒序
3. 如果有多个学历 → 每个占一行
4. 如有 GPA 且 >= 3.5/4.0 → 标注
5. 如有相关课程且与 target 高度相关 → 可选标注（紧凑模板中省略）
6. 学术模板中：教育前置，可展开描述研究方向、导师、论文
7. 其他模板中：简洁一行即可

## Trim Priority
1. Remove course/GPA details first
2. Condense to single line per entry
3. Keep only highest degree if multiple

## 输出格式
纯 Markdown，写入 sections/education.md

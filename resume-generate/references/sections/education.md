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

## Kami 模板输出格式

当 `--template kami` 时，education 输出使用结构化标记：

```markdown
<!-- KAMI:EDUCATION -->
**学校：** 北京邮电大学
**学院：** 计算机学院
**专业：** 计算机科学与技术
**时间：** 2015.09 - 2019.06
**判断：** 放弃保研直接就业
<!-- /KAMI:EDUCATION -->
```

**Kami education 规则：**
- 一行式，包含学校 + 学院 + 专业 + 一句判断性描述
- 判断性描述（如"放弃保研"、"跨专业"）比 GPA 更能展示自我方向感
- 如无判断性描述，省略该字段

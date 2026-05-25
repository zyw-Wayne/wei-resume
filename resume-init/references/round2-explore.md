# Round 2: Exploratory Mining

## Entry Check

- Verify `profile.basic.name` exists in `state.json` (round 1 completed)
- If not, prompt: "请先完成第一轮基础信息采集 (wei-resume init)"

## Topic Sequence

Progressive questioning — each answer informs the next question.

### a. 技术方向
> 你最擅长/最想深耕的技术方向是什么？

Follow-up: "这个方向你做了多久？目前处于什么水平？"

### b. 团队角色
> 在团队里你通常扮演什么角色？

Options hint: IC / Tech Lead / 架构师 / 全栈 / 其他
Follow-up: "团队多大规模？你负责的范围是？"

### c. 骄傲时刻
> 最近一年最让你有成就感的技术工作是什么？

Follow-up (two levels max):
1. "为什么觉得这个特别有成就感？" (why)
2. "具体是怎么做到的？遇到了什么困难？" (how)

### d. 技术亮点
> 你觉得自己相比同级工程师，最突出的技术优势是什么？

Follow-up: "能举个具体的例子吗？"

### e. 证书/开源/文章/演讲
Quick confirmation for each sub-item:
- "有技术相关的证书吗？（AWS/K8s/PMP 等）"
- "有维护或深度参与的开源项目吗？"
- "写过技术博客或文章吗？大概多少篇？"
- "有过技术演讲或分享经历吗？"

### f. 目标方向
> 下一份工作想去什么类型的公司/做什么方向？

Follow-up: "为什么想往这个方向？有什么特别看重的？"

## Follow-Up Strategy

- For each answer, ask **at most ONE** follow-up to deepen understanding
- Maximum 2 levels deep (original question -> follow-up -> one more follow-up)
- If user gives a detailed answer, skip the follow-up and move on

## Interruptible

- If user says "够了", "下一轮", "跳过剩余", stop immediately
- Save all data collected so far
- Remaining topics can be filled via `--update`

## Output Mapping

| Topic | state.json Path |
|---|---|
| a. 技术方向 | `profile.strengths.tech_direction` |
| b. 团队角色 | `profile.strengths.team_role` |
| c. 骄傲时刻 | `profile.strengths.highlights[]` |
| d. 技术亮点 | `profile.strengths.highlights[]` |
| e. 证书 | `profile.strengths.certifications[]` |
| e. 开源 | `profile.strengths.open_source[]` |
| e. 文章 | `profile.strengths.articles[]` |
| e. 演讲 | `profile.strengths.talks[]` |
| f. 目标方向 | `target_direction` |

## Round Completion

1. Show summary of new data collected in this round
2. Ask "有要补充或修改的吗？"
3. Mark `meta.rounds_completed` += "round2"
4. Update `meta.last_updated` timestamp

# Round 3: Deep Interview

## Entry Check

- Verify `meta.rounds_completed` includes both "round1" and "round2"
- If not, prompt: "请先完成前两轮采集再进入深度访谈"

## Adaptive Topic Pool

Select **10-15 questions** from the pools below. Do NOT ask all questions — adapt based on previous answers and what is already in `state.json`.

### Pool A: 做事风格与解题方式

1. 拿到一个模糊需求时，你的第一反应是什么？会怎么拆解？
2. 遇到过最棘手的技术问题是什么？你是怎么一步步排查的？
3. 你通常怎么做技术决策？会考虑哪些因素？
4. 你和 AI 的协作模式是什么？日常工作中怎么用 AI？

> Question A4 is the natural bridge to AI topics — transition smoothly, don't isolate AI as a separate block.

### Pool B: AI 认知与实践

5. 日常用哪些 AI 工具？大概占编码/工作时间的百分之多少？
6. 有没有实践过这些 AI 开发范式？Vibe Coding / Context Engineering / Agent Development
7. 你觉得 AI 会怎样影响软件工程师这个职业？你怎么应对？
8. 有没有用 AI 做过让你觉得 "这以前不可能" 的事情？

### Pool C: 协作与自我认知

9. 在团队协作中，你通常扮演什么角色？推动者/协调者/执行者？
10. 如果让同事用三个词形容你，你觉得会是哪三个？
11. 你觉得自己最大的技术优势是什么？（不是技术栈，而是能力层面）
12. 你有什么明确的成长方向或想补的短板吗？

### Pool D: 职业叙事

13. 从第一份工作到现在，有没有一条贯穿始终的主线？
14. 如果用一句话总结你的职业特点，你会怎么说？
15. 五年后你希望自己在做什么？

## Selection Strategy

- Each pool **maximum 3-4 questions**, total across all pools **<= 15**
- AI topics (Pool B) should emerge naturally from work style discussion (Pool A Q4)
- If user already discussed AI tools/practices in round 2, **skip or minimize Pool B**
- If `profile.strengths.team_role` is already rich, reduce Pool C team questions
- Prioritize pools where `state.json` has the least data

## Interviewing Style

- Conversational, not interrogative
- Extract traits/keywords **immediately** after each answer and write to `state.json`
- If an answer is surface-level, ask ONE follow-up: "能展开说说吗？" or "有具体例子吗？"
- If user gives a rich answer, acknowledge and move on — don't over-probe

## Output Mapping

| Pool | state.json Path |
|---|---|
| A1-A3 | `profile.persona.work_style`, `profile.persona.problem_solving` |
| A4, B5-B8 | `profile.ai.tools`, `profile.ai.skills`, `profile.ai.cognition`, `profile.ai.workflow` |
| C9-C10 | `profile.persona.collaboration`, `profile.persona.self_image` |
| C11-C12 | `profile.persona.growth_area` |
| D13-D14 | `profile.persona.career_narrative`, `profile.persona.tagline` |
| D15 | `profile.persona.future_vision` |

## Synthesis

After all selected questions are answered:

1. Review all collected persona data
2. If `profile.persona.tagline` was not explicitly stated by the user, **generate one** based on the interview:
   - Format: one sentence, < 20 characters Chinese or < 50 characters English
   - Example: "用工程思维解决模糊问题的全栈工程师"
3. Show the generated tagline and ask for confirmation or revision

## Round Completion

1. Show full persona summary (work style, AI profile, collaboration style, career narrative)
2. Ask "这些描述准确吗？有要调整的地方吗？"
3. Mark `meta.rounds_completed` += "round3"
4. Update `meta.last_updated` timestamp
5. Print: "个人画像采集完成！可以运行 `wei-resume scan` 开始代码扫描了。"

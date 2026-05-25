# Data Accuracy Rules

These rules are loaded into every Phase 2 section generation context.
They govern what data can be stated as fact in the resume.

## Rule 1: Source Verification

Only use data points marked with one of these verification levels:
- `git_verifiable`: data can be confirmed from git history (commits, diffs, file counts)
- `code_inferable`: data can be reasonably inferred from code structure
- `user_confirmed`: user explicitly confirmed this number or claim

## Rule 2: Unfilled Placeholders

When a data field exists but has no verified value, use vague wording:
- ✅ "显著提升了系统性能"
- ❌ "系统性能提升 300%"
- ✅ "Significantly improved system performance"
- ❌ "Improved system performance by 300%"

## Rule 3: Allowed Reasonable Inference

Git/code evidence supports factual descriptions, not fabricated metrics:
- Git shows new `test/` directory + 47 test files → ✅ "从零搭建测试体系" (factual)
- Same evidence → ❌ "测试覆盖率达到 80%" (fabricated number)
- Git shows migration from Express to Fastify → ✅ "主导框架迁移" (factual)
- Same evidence → ❌ "迁移后延迟降低 60%" (fabricated metric)

## Rule 4: Before/After Differences

Only describe the verifiable portion of before/after comparisons:
- ✅ "将单体服务拆分为 12 个微服务" (if git confirms 12 service repos)
- ❌ "拆分后可用性从 99.5% 提升到 99.99%" (unless user confirmed)

## Rule 5: User-Confirmed Numbers

Numbers explicitly confirmed by the user can be stated precisely:
- User says "DAU was 500K" → ✅ "服务日活 50 万用户"
- User says "about half a million" → ✅ "服务约 50 万日活用户" (keep the "约")

## Rule 6: When in Doubt, Understate

Prefer understated over overstated. Credibility is more valuable than impressiveness.

## NEVER Fabricate

The following categories must NEVER contain fabricated numbers:
- Performance metrics (latency, throughput, uptime)
- Availability / SLA numbers
- Business metrics (revenue, conversion rate, user count)
- Specific percentages of any kind
- Team size or headcount
- Cost savings or ROI figures

# state.json 完整 Schema 定义

> `~/resumes/state.json` — 所有技能共享的唯一真相源。

## 两层数据架构

```
state.json（紧凑层，目标 <200 行，始终可全量读取）
  ├── _meta           → 版本与迁移元数据
  ├── profile         → 内联完整数据（~60 行）
  ├── projects[]      → 内联摘要（每项目 ~20 行）+ detail_path 引用
  ├── targets[]       → 内联摘要（每岗位 ~8 行）+ detail_path 引用
  ├── outputs[]       → 路径引用 + 编辑元数据
  └── config          → 全局默认值

detail_path 指向的文件（详细层，按需读取）
  ├── projects/*/analysis.json   → 完整代码分析（可能 200+ 行）
  ├── projects/*/context.md      → 用户补充的项目上下文
  └── targets/*.json             → 完整 JD 解析
```

原则：state.json 控制在 200 行以内。任何技能读一次即可获得全局视图，需要深入时再按 detail_path 读取。

## 读写契约

| 技能 | 读 state 字段 | 写 state 字段 |
|------|-------------|-------------|
| resume-init | _meta（检查存在性） | profile.* |
| resume-scan | profile.git_authors | projects[] |
| resume-target | profile + projects（匹配用） | targets[] |
| resume-import | profile（合并用） | profile.* / projects[] |
| resume-generate | profile + projects + targets + config | outputs[] |
| wei-resume (路由) | _meta（迁移检查） | _meta（迁移后更新） |

## 完整 JSON 结构

```json
{
  "_meta": {
    "schema_version": 3,
    "created_at": "2026-04-18T10:00:00Z",
    "updated_at": "2026-04-18T14:30:00Z",
    "migrations_applied": []
  },

  "profile": {
    "basic": {
      "name_zh": "zhangsan",
      "name_en": "sanzhang",
      "phone": "138xxxx",
      "email": "zhangsan@example.com",
      "links": { "github": "...", "blog": "...", "linkedin": "..." }
    },
    "education": [
      {
        "school": "XX大学",
        "major": "计算机科学",
        "degree": "本科",
        "period": "2016-2020"
      }
    ],
    "career": [
      {
        "company": "XX科技",
        "title": "高级开发工程师",
        "period": "2022.03-至今",
        "highlights": []
      }
    ],
    "git_authors": ["Wei", "zhangsan", "wei@company.com"],
    "strengths": {
      "tech_direction": "后端架构",
      "team_role": "Tech Lead",
      "highlights": ["主导了XX架构迁移"],
      "certifications": [],
      "patents": [],
      "open_source": [],
      "articles": [],
      "talks": []
    },
    "target_direction": {
      "direction": ["后端", "架构"],
      "company_type": "不限"
    },
    "persona": {
      "work_style": "先拆解子问题再动手",
      "problem_solving": "擅长从源码层面定位根因",
      "collaboration": "倾向于用数据说服",
      "drivers": ["技术成长", "自由度", "业务有意义"],
      "self_image": ["系统性思维", "学习速度快"],
      "growth_area": "跨团队推动落地",
      "career_narrative": "从业务开发到架构，追求系统性解决问题",
      "tagline": "擅长在复杂系统中建立清晰秩序的工程师",
      "stories": []
    },
    "ai": {
      "tools": {
        "primary": "Claude Code",
        "secondary": ["Cursor"],
        "usage_ratio": "60%"
      },
      "skills": [],
      "cognition": {
        "stage": "AI Native",
        "core_view": "..."
      },
      "workflow": "需求拆解 → Context 准备 → AI 生成 → Review → 迭代"
    },
    "social_proof": []
  },

  "projects": [
    {
      "name": "my-project",
      "path": "/path/my-project",
      "scanned_at": "2026-04-18T10:00:00Z",
      "git_head": "abc123def",
      "scan_params": {
        "authors": ["lisi", "zhangsan"],
        "since": "2024-01-01",
        "until": null
      },
      "summary": {
        "type": "web-fullstack",
        "architecture": "前后端分离，Go 微服务 + React SPA",
        "tech_stack": ["Go", "TypeScript", "React", "gRPC", "K8s"],
        "author_period": "2024-03 ~ 2025-01",
        "author_ratio": "38%（团队 6 人）",
        "core_modules": [
          "services/order/（主负责人）",
          "pkg/middleware/（独立开发）"
        ],
        "before_after": {
          "verifiable": ["模块数 3→8", "测试文件 0→47 个"],
          "needs_confirmation": ["测试覆盖率需用户确认具体数字"]
        },
        "code_quality": {
          "engineering": "高",
          "design": "清晰三层",
          "security": "JWT+RBAC"
        },
        "ai_signals": {
          "level": 3,
          "label": "AI Native",
          "details": ["CLAUDE.md", "Skills×2"]
        }
      },
      "value_points": [
        {
          "score": 95,
          "desc": "测试体系从零搭建（测试文件从 0 增至 47 个）",
          "category": "engineering",
          "data_source": "git_verifiable",
          "placeholders": ["具体覆盖率百分比需用户确认"]
        }
      ],
      "detail_path": "~/resumes/projects/my-project/analysis.json"
    }
  ],

  "targets": [
    {
      "name": "字节-架构",
      "company": "字节跳动",
      "level": "P6-P7",
      "tech_required": ["Go", "Kubernetes", "微服务"],
      "tech_preferred": ["Rust", "分布式存储"],
      "keywords": ["高可用", "性能优化", "架构设计"],
      "match_score": 81,
      "match_gaps": ["Rust（preferred）", "分布式存储经验不足"],
      "detail_path": "~/resumes/targets/字节-架构.json"
    }
  ],

  "outputs": [
    {
      "id": "out-20260418-001",
      "generated_at": "2026-04-18T14:30:00Z",
      "projects": ["my-project"],
      "target": "字节-架构",
      "style": "all",
      "template": "modern",
      "lang": "zh",
      "plan_path": "~/resumes/output/my-project--字节-架构--20260418/resume-plan.json",
      "sections_path": "~/resumes/output/my-project--字节-架构--20260418/sections/",
      "files": {
        "tech": "~/resumes/output/my-project--字节-架构--20260418/resume-tech.md",
        "hr": "~/resumes/output/my-project--字节-架构--20260418/resume-hr.md",
        "full": "~/resumes/output/my-project--字节-架构--20260418/resume-full.md"
      },
      "edits": {
        "manual_modifications": [
          {
            "section": "projects",
            "modified_at": "2026-04-18T15:00:00Z",
            "description": "用户修改了订单项目的第 2 条 bullet"
          }
        ],
        "preserve_on_regenerate": true
      },
      "quality_score": 87
    }
  ],

  "config": {
    "default_lang": "zh",
    "default_style": "all",
    "default_template": "modern",
    "default_format": ["md", "html"],
    "default_pages": "auto",
    "default_color": "#2563eb",
    "auto_ats": true
  }
}
```

## 字段说明

### _meta

| 字段 | 类型 | 说明 |
|------|------|------|
| schema_version | number | 当前 schema 版本，最新为 3 |
| created_at | ISO 8601 | state.json 创建时间 |
| updated_at | ISO 8601 | 最近一次写入时间 |
| migrations_applied | string[] | 已执行的迁移记录，如 `"v2→v3 at 2026-04-19"` |

### profile.basic

| 字段 | 类型 | 说明 |
|------|------|------|
| name_zh | string | 中文姓名 |
| name_en | string | 英文/拼音姓名 |
| phone | string | 手机号（隐私敏感） |
| email | string | 邮箱 |
| links | object | github / blog / linkedin 等链接 |

### profile.education[]

| 字段 | 类型 | 说明 |
|------|------|------|
| school | string | 学校名称 |
| major | string | 专业 |
| degree | string | 学历（本科/硕士/博士） |
| period | string | 起止年份 |

### profile.career[]

| 字段 | 类型 | 说明 |
|------|------|------|
| company | string | 公司名称 |
| title | string | 职位 |
| period | string | 起止时间 |
| highlights | string[] | 工作亮点 |

### profile.strengths

技术方向、团队角色、亮点、证书、专利、开源贡献、文章、演讲。所有数组字段初始为 `[]`。

### profile.target_direction

求职意向方向和公司类型偏好。注意：v1 schema 中此字段名为 `target`，v2 迁移时重命名。

### profile.persona

深度访谈提取的人格特质，用于生成个性化描述。`stories[]` 存放 STAR 格式故事素材。

### profile.ai

AI 工具使用、技能实践、认知阶段、工作流。用于 ai-capability section 生成。

### profile.social_proof[]

社交平台数据：`{ platform, metrics, url }`。metrics 结构因平台而异。

### projects[]

| 字段 | 类型 | 说明 |
|------|------|------|
| name | string | 项目名称 |
| path | string | 本地路径 |
| scanned_at | ISO 8601 | 扫描时间 |
| git_head | string | 扫描时的 HEAD commit hash，用于增量更新判断 |
| scan_params | object | 扫描参数：authors / since / until |
| summary | object | 项目摘要（内联，~20 行） |
| summary.before_after | object | `{verifiable: string[], needs_confirmation: string[]}` (v3) |
| value_points[] | object | 简历价值点，含 score / desc / category / data_source / placeholders |
| detail_path | string | 完整分析文件路径 |

### targets[]

| 字段 | 类型 | 说明 |
|------|------|------|
| name | string | 岗位标识（如 "字节-架构"） |
| company | string | 公司名称 |
| level | string | 级别 |
| tech_required | string[] | 必须技术栈 |
| tech_preferred | string[] | 加分技术栈 |
| keywords | string[] | JD 高频关键词 |
| match_score | number | 匹配度（0-100） |
| match_gaps | string[] | 差距点列表 (v3) |
| detail_path | string | 完整 JD 解析文件路径 |

### outputs[]

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 输出标识（格式 `out-YYYYMMDD-NNN`） |
| generated_at | ISO 8601 | 生成时间 |
| projects | string[] | 使用的项目列表 |
| target | string | 目标岗位名称 |
| style | string | 风格（tech/hr/full/all） |
| template | string | 模板名称 |
| lang | string | 语言（zh/en） |
| plan_path | string | Phase 1 规划文件路径 (v3) |
| sections_path | string | Phase 2 逐段输出目录路径 (v3) |
| files | object | 最终输出文件路径映射 |
| edits | object | 手动编辑记录 (v3) |
| edits.manual_modifications[] | object | 每条含 section / modified_at / description |
| edits.preserve_on_regenerate | boolean | 重新生成时是否保留手动修改 |
| quality_score | number | 简历质量评分（0-100） |

### config

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| default_lang | string | `"zh"` | 默认语言 |
| default_style | string | `"all"` | 默认风格 |
| default_template | string | `"modern"` | 默认模板 |
| default_format | string[] | `["md","html"]` | 默认输出格式 |
| default_pages | string | `"auto"` | 默认页数 |
| default_color | string | `"#2563eb"` | 默认主色调 |
| auto_ats | boolean | `true` | 是否自动生成 ATS 版 |

## 降级行为

state.json 缺失字段时 resume-generate 不报错，尽力生成并提示补充：

| 有 | 缺 | 行为 |
|---|---|------|
| profile | projects | 用 profile 生成基础简历，提示运行 scan |
| projects | targets | 生成通用版，提示运行 target |
| projects + targets | profile.persona | 正常生成，自我评价用通用模板 |
| 无任何数据 | — | 提示运行 `wei-resume init` |

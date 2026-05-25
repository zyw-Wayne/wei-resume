# Historical Resume Import Strategy

## Supported Formats

PDF, DOCX, Markdown, plain text, HTML

## Import Flow

### Step 1: Read File Content
- **PDF**: Use Bash tool for text extraction (`pdftotext` or similar); if unavailable, ask user to paste content
- **DOCX**: Use Bash tool (`pandoc` or `textutil` on macOS) to convert to plain text
- **Markdown / TXT / HTML**: Read directly with Read tool

### Step 2: Structure Extraction
Parse the imported content into standard sections:
- 基本信息 (name, contact, links)
- 教育经历 (education)
- 工作经历 (experience)
- 技术技能 (skills)
- 项目经历 (projects)
- 其他 (certifications, publications, awards)

### Step 3: Merge Strategy
For each extracted field, compare with existing `state.json`:

| Scenario | Action |
|---|---|
| Field empty in state.json | Fill directly from import |
| Field already filled | Show diff, ask user which to keep |
| New info not in schema | Suggest where to store, ask for confirmation |
| Conflicting data | Present both versions, let user decide |

## Post-Import Diagnosis

After merging, analyze the imported resume and report issues:

### Quantification Check
- Count bullet points with numbers/metrics vs. total bullets
- Report: "你的简历缺少量化数据（X/Y 条 bullet 有数字）"

### Specificity Check
- Flag vague phrases: "负责", "参与", "协助" without concrete details
- Report: "技术栈描述较泛，建议通过 scan 补充具体项目证据"

### Achievement Orientation Check
- Distinguish responsibility descriptions from achievement statements
- Report: "工作经历只有职责描述，缺少成果展示"

### Timeline Check
- Detect gaps > 3 months between positions
- Report: "X 到 Y 之间有 N 个月空档，是否需要说明？"

## Follow-Up Questions (based on diagnosis)

| Diagnosis | Follow-Up |
|---|---|
| Missing quantification | "这个项目的用户量/性能提升/团队规模大概是多少？" |
| Vague descriptions | "能具体说说你在这个项目中做了什么吗？" |
| Timeline gaps | "这段时间你在做什么？（学习/旅行/创业 都可以）" |
| No achievement statements | "这段工作最大的成果是什么？有没有可量化的指标？" |

## Quality Scoring (0-100)

Score the imported resume on four dimensions (each 0-25):

| Dimension | Criteria |
|---|---|
| 量化程度 (Quantification) | % of bullets with metrics |
| 具体性 (Specificity) | Concrete tech/tools/methods mentioned |
| 成果导向 (Achievement) | Results vs. responsibilities ratio |
| ATS 友好度 (ATS-friendliness) | Keywords, formatting, section clarity |

Display total score and per-dimension breakdown with improvement suggestions.

## Output

1. Merged data written to relevant `state.json` sections
2. Original file archived to `~/resumes/imports/` with timestamp
3. Diagnosis report displayed to user
4. Mark `meta.rounds_completed` += "import"
5. Update `meta.last_updated` timestamp

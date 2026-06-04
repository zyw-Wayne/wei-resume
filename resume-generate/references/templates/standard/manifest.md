---
name: standard
description: 统一标准模板，通过 variant 切换三种风格：modern（双栏科技蓝）、classic（单栏衬线）、compact（紧凑高密度）
variants:
  - name: modern
    layout: two-column
    color_scheme: tech-blue
    font: sans-serif
    header_style: left-aligned-with-color-bar
    skills_display: grouped-badges
    page_limit: 3
  - name: classic
    layout: single-column
    color_scheme: monochrome
    font: serif
    header_style: centered
    skills_display: inline-list
    page_limit: 3
  - name: compact
    layout: single-column
    color_scheme: minimal
    font: sans-serif-condensed
    header_style: single-line
    skills_display: comma-separated
    page_limit: 3
shared_defaults:
  date_format: "YYYY.MM"
  bullet_style: hierarchical
  project_format: background-paragraph-plus-subbullets
  experience_format: overview-bullets
  sections_order: [header, summary, skills, experience, projects, education, ai-capability, extras]
---

# Standard Template

三个变体共用一套 HTML + CSS，通过 `<body class="modern|classic|compact">` 切换。

## 变体选择

| 用户说 | variant | 视觉特征 |
|--------|---------|---------|
| "现代" / "科技感" / "双栏" | modern | 双栏 30/70，sans-serif，蓝色强调，badge 技能 |
| "经典" / "正式" / "打印" | classic | 单栏，serif，黑白，居中 header，inline 技能 |
| "紧凑" / "一页" / "信息密集" | compact | 单栏，condensed，9.5pt，1.3 行高，逗号分隔技能 |

## CSS 变量切换机制

三个变体的差异全部通过 CSS 变量实现，不需要三套独立 CSS：

```css
body.modern  { /* 双栏 + 蓝色 + sans-serif */ }
body.classic { /* 单栏 + 黑白 + serif */ }
body.compact { /* 单栏 + 紧凑 + condensed */ }
```

共用的组件样式（.entry, .entry-bullets, .education-entry 等）只写一次，变体只覆盖变量值和少量布局差异。

## Section 差异

| Section | modern | classic | compact |
|---------|--------|---------|---------|
| skills | grouped-badges（左侧栏） | inline-list（正文流） | comma-separated（单行） |
| header | 左对齐+色条（右侧栏） | 居中 | 单行两端对齐 |
| extras | ✅ | ✅ | ❌ 省略 |

## Phase 2 行为

Phase 2 为每个 variant 独立生成 section markdown（sections/standard/{variant}/{style}/{id}.md）。三个 variant 共享相同的 section plugin，只有 skills 的输出格式因 `skills_display` 不同而变化。

## Phase 3 行为

Phase 3 读取 `template-{cn|en}.html`，将 `<body>` 替换为 `<body class="{variant}">`，然后按标准流程填充内容。CSS 已内联在模板中，无需额外文件。

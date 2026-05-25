---
name: academic
description: 学术风格，强调论文/专利/研究成果，无数据时自动跳过学术 section
layout: single-column
page_limit: 3
color_scheme: monochrome
font: serif
sections_order: [header, summary, education, publications, research, projects, skills, experience, ai-capability, extras]
section_divider: section-title-underline
header_style: academic-centered
skills_display: categorized-list
date_format: "YYYY"
extra_sections:
  publications:
    data_keys: ["profile.strengths.patents", "profile.strengths.articles"]
    format: citation-style
    skip_if_empty: true
  research:
    data_keys: ["projects[].summary where type=research"]
    format: abstract-style
    skip_if_empty: true
bullet_style: hierarchical
project_format: background-paragraph-plus-subbullets
experience_format: overview-bullets
---

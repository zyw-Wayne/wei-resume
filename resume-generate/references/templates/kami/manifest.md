---
name: kami
layout: two-column-aware
page_limit: 2
color_scheme: parchment-ink
font: serif
accent_color: "#1B365D"
header_style: left-border-accent
skills_display: label-description-rows
section_divider: left-border-accent
date_format: YYYY.MM
bullet_style: hierarchical
project_format: three-part-table
experience_format: three-step-timeline
print_friendly: true
source: kami-skill
---

# Kami Template Manifest

Visual identity: warm parchment canvas (`#f5f4ed`), ink-blue accent (`#1B365D`), serif-led hierarchy (TsangerJinKai02 for CN, Charter for EN), tight editorial rhythm.

## Sections Order

Kami uses a two-page structure. code2resume maps its sections into this layout:

### Page 1 (always present)
1. **header** — name + alias + role + contact (left-border accent)
2. **metrics** — 4 key-number cards (from state.json persona metrics)
3. **summary** — ~80 chars CN / ~50 words EN
4. **experience** — 3-step timeline + 3-5 project blocks (three-part table: 角色/动作/结果)

### Page 2 (conditional, sections removed if no data)
5. **open-source** — intro + 2-column grid of 6 projects + highlight (skip if empty)
6. **ai-capability** — conviction cards, 3-column grid (skip if AI level = 0)
7. **influence** — handle strip + articles/talks grid (skip if empty)
8. **skills** — 5 rows with label + description
9. **education** — single row, judgment-flavored note

## Placeholder Convention

All dynamic content uses `{{SLOT_NAME}}` placeholders. Phase 3 fills these from section markdown output. The template HTML contains comment markers `<!-- SECTION: <id> -->` to indicate where each section's content is injected.

## Key Design Decisions

- **2 pages, not 3**: kami enforces strict 2-page A4. code2resume's default 3-page budget is compressed.
- **Dense variant**: `class="resume--dense"` on `<body>` when 5+ projects on page 1.
- **No synthetic bold**: heading weight is real 500, not CSS bold on regular face.
- **`.hl` for emphasis**: brand-blue color on numbers/distinctive nouns only, never adjectives.
- **Break-inside avoid**: on `.project`, `.metric`, `.os-item`, `.conv-card`, `.skill-row`, `.edu-row`.

## PDF Generation

Use WeasyPrint (not browser print-to-PDF):
```bash
python3 -c "from weasyprint import HTML; HTML('resume.html').write_pdf('out.pdf')"
```

WeasyPrint respects `@page` rules, `break-inside: avoid`, `widows`/`orphans`, and font-face declarations. Browser print-to-PDF does not guarantee these.

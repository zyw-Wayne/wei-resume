# ATS Optimization Rules

Rules for generating ATS (Applicant Tracking System) friendly resume versions.
Loaded during Phase 2 when `--ats` flag is set, and during Phase 3 ATS assembly.

## Keyword Handling

1. Extract required and preferred keywords from target JD
2. Ensure each required keyword appears naturally at least once in the resume body
3. Tech stack keywords must preserve original casing: `TypeScript` not `typescript`, `Node.js` not `nodejs`
4. Do not keyword-stuff — each keyword should appear in meaningful context

## Structure Rules

5. Avoid nested tables — ATS parsers frequently fail on complex table structures
6. Use standard section titles:
   - Chinese: "个人简介", "工作经历", "项目经历", "专业技能", "教育背景"
   - English: "Summary", "Work Experience", "Projects", "Skills", "Education"
   - ❌ Avoid creative titles: "我的旅程", "My Journey", "技术宇宙", "Tech Universe"
7. Use unified date format throughout:
   - Chinese: `2022.03 - 2024.01` or `2022年3月 - 2024年1月`
   - English: `Mar 2022 - Jan 2024`
   - Pick one format and use it consistently

## Formatting Rules

8. No images, icons, or emoji in ATS version — parsers cannot read them
9. Plain text structure: use line breaks and simple markers (-, *) instead of markdown formatting
10. Contact information on separate lines, not comma-separated:
    ```
    张三
    zhangsan@email.com
    +86 138-0000-0000
    github.com/zhangsan
    ```

## Output Format

- ATS version should be generated as plain `.md` or `.txt`
- No bold, italic, links, or other rich formatting
- Section headers in ALL CAPS or with simple underline markers for clarity

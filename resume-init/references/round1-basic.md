# Round 1: Deterministic Information Collection

## Pre-Check

1. Read existing `state.json` — if it exists, show a summary of what is already filled
2. Ask: "要更新哪些？还是全部重新填写？"
3. If no `state.json`, start fresh

## Collection Items

Ask **one item at a time**. Accept partial answers. Never block on incomplete fields.

### 1. 姓名
- 中文名
- 英文名 / 拼音
- **Required** — the only truly mandatory field

### 2. 手机号
- Validate format (Chinese mobile: 1xx-xxxx-xxxx or international with country code)

### 3. 邮箱
- Validate basic email format (user@domain.tld)

### 4. 个人链接
- GitHub URL
- Blog / 个人网站
- LinkedIn URL
- 其他链接（可多个）

### 5. 教育经历（支持多条）
Each entry:
- 学校名称
- 专业
- 学位（本科/硕士/博士/其他）
- 时间段（yyyy.mm - yyyy.mm）
- Validate: end date >= start date

### 6. 工作经历概览（支持多条）
Each entry:
- 公司名称
- 职位/头衔
- 时间段（yyyy.mm - yyyy.mm 或 至今）
- 一句话亮点（该段经历中最值得说的一件事）

### 7. Git Author 标识
- 用于代码扫描时匹配贡献
- Smart default: run `git log --format='%aN <%aE>' | sort -u` to suggest authors
- Allow multiple identities (work email, personal email, etc.)

## Skip Rules

- User can say "跳过" for any field except 姓名
- Skipped fields are written as `null` in `state.json` — can be filled later via `--update`

## Output Mapping

Write each field immediately after collection:

| Collected Item | state.json Path |
|---|---|
| 姓名 | `profile.basic.name_cn`, `profile.basic.name_en` |
| 手机号 | `profile.basic.phone` |
| 邮箱 | `profile.basic.email` |
| 个人链接 | `profile.basic.links{}` |
| 教育经历 | `profile.education[]` |
| 工作经历 | `profile.career[]` |
| Git authors | `git_authors[]` |

## Round Completion

After all items collected (or skipped):
1. Show summary of collected data
2. Ask "有要修改的吗？"
3. Mark `meta.rounds_completed` += "round1"
4. Update `meta.last_updated` timestamp

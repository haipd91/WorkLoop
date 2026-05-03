# Design: simplify-command-names

## Cơ chế hoạt động

Claude Code load slash commands từ:
- `.claude/commands/<name>.md` → `/name`
- `.claude/commands/<namespace>/<name>.md` → `/namespace:name`

Để có `/propose`, `/code`, `/explore`, `/archive` — chỉ cần tạo files tương ứng ở `.claude/commands/` với nội dung giống hệt bản `opsx:`.

## Mapping files

```
Tạo mới:                              Copy nội dung từ:
──────────────────────────────────    ──────────────────────────────────
.claude/commands/propose.md     ←     .claude/commands/opsx/propose.md
.claude/commands/code.md        ←     .claude/commands/opsx/apply.md
.claude/commands/explore.md     ←     .claude/commands/opsx/explore.md
.claude/commands/archive.md     ←     .claude/commands/opsx/archive.md
```

## Thay đổi frontmatter

Mỗi file mới cần cập nhật frontmatter `name` cho rõ ràng:

```yaml
# propose.md
name: "Propose"
description: Propose a new change - create it and generate all artifacts in one step

# code.md  
name: "Code"
description: Implement tasks from an OpenSpec change

# explore.md
name: "Explore"
description: Enter explore mode - think through ideas before building

# archive.md
name: "Archive"
description: Archive a completed change
```

## Workflow commands sau khi đổi

```
/brief        → tạo brief.md (skill mới — đã ngắn)
/propose      → tạo spec + plan + tasks
/tdd          → tạo test files (skill mới — đã ngắn)
/code         → implement tasks
/explore      → explore mode
/archive      → archive change xong

opsx: variants vẫn hoạt động song song (không xóa)
```

## Không cần thay đổi gì khác

- Nội dung instructions bên trong commands: giữ nguyên
- Skills trong `.claude/skills/`: không đụng
- `openspec/config.yaml`: không đụng

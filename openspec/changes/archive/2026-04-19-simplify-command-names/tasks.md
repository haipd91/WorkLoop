# Tasks: simplify-command-names

## T1 — Tạo 4 command files mới

Tạo các files sau tại `.claude/commands/`:

- `propose.md` — copy từ `opsx/propose.md`, đổi frontmatter `name: "Propose"`
- `code.md` — copy từ `opsx/apply.md`, đổi frontmatter `name: "Code"` và `description` thành "Implement tasks from an OpenSpec change"
- `explore.md` — copy từ `opsx/explore.md`, đổi frontmatter `name: "Explore"`
- `archive.md` — copy từ `opsx/archive.md`, đổi frontmatter `name: "Archive"`

**Done khi:** 4 files tồn tại, nội dung instructions giống hệt bản `opsx:`, chỉ khác frontmatter

---

## T2 — Verify commands hoạt động

Chạy thử mỗi command mới và xác nhận:
- `/propose` → load đúng, hoạt động như `/opsx:propose`
- `/code` → load đúng, hoạt động như `/opsx:apply`
- `/explore` → load đúng, hoạt động như `/opsx:explore`
- `/archive` → load đúng, hoạt động như `/opsx:archive`
- `/opsx:propose`, `/opsx:apply`, ... vẫn hoạt động bình thường

**Done khi:** cả 8 commands (4 mới + 4 cũ) đều load được

---

## T3 — Cập nhật team-ai-workflow.md

Cập nhật section 6 Mapping Công Cụ và Visual Workflow (section 5) dùng tên commands mới:
- `/opsx:propose` → `/propose`
- `/opsx:apply` → `/code`
- `/opsx:explore` → `/explore`
- `/opsx:archive` → `/archive`

Thêm ghi chú: `opsx:` variants vẫn hoạt động song song.

**Done khi:** workflow doc dùng tên commands mới nhất quán

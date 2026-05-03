## 1. Tạo skill /tdd

- [x] 1.1 Tạo `.claude/skills/tdd/SKILL.md` với frontmatter đúng (name, description, compatibility)
- [x] 1.2 Implement bước đọc toàn bộ artifacts: proposal.md, specs/, design.md, tasks.md
- [x] 1.3 Implement framework detection từ project files (package.json, pyproject.toml, go.mod, CLAUDE.md)
- [x] 1.4 Implement generate test files vào `openspec/changes/<name>/tests/` — happy path, edge cases, error cases
- [x] 1.5 Implement human review loop: hiển thị files đã tạo, hỏi approve qua AskUserQuestion, hỗ trợ chỉnh sửa iterative
- [x] 1.6 Sau approve: confirm save path, gợi ý chạy `/code`

## 2. Cập nhật skill /code

- [x] 2.1 Thêm bước kiểm tra `openspec/changes/<name>/tests/` trước khi implement
- [x] 2.2 Nếu `tests/` tồn tại: đọc và dùng làm context bổ sung cho từng task
- [x] 2.3 Nếu `tests/` không tồn tại: tiếp tục bình thường, không block

## 3. Cập nhật team-ai-workflow.md

- [x] 3.1 Thêm `/tdd` vào Visual Workflow (section 5) giữa Specification và Implementation
- [x] 3.2 Cập nhật section 6 Mapping Công Cụ thêm `/tdd`
- [x] 3.3 Thêm gate "approve test files" vào section 8 Approval Gates

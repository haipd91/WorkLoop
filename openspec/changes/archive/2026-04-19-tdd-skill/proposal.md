## Why

Bước `/code` implement trực tiếp từ tasks mà không có test nào được định nghĩa trước. AI không có done criteria rõ ràng — không biết implement đúng hay sai cho đến khi xong. Team cần một bước tạo test files trước khi code chạy, để human biết kỳ vọng và AI có input rõ ràng.

## What Changes

- **ADDED**: Skill `/tdd` mới — đọc toàn bộ artifacts của một change và generate test files
- **MODIFIED**: Skill `/code` — tự động đọc `openspec/changes/<name>/tests/` nếu có làm context bổ sung trước khi implement

## Capabilities

### New Capabilities
- `tdd-skill`: Generate test files từ artifacts (proposal, specs, design, tasks) vào `openspec/changes/<name>/tests/`, hỗ trợ human review và approve trước khi chuyển sang `/code`

### Modified Capabilities
- `code-skill`: Đọc `tests/` folder nếu tồn tại làm context bổ sung khi implement

## Impact

- `.claude/skills/tdd/SKILL.md` — file mới
- `.claude/skills/code/SKILL.md` — sửa bổ sung bước đọc `tests/`
- `docs/team-ai-workflow.md` — cập nhật workflow thêm bước `/tdd`

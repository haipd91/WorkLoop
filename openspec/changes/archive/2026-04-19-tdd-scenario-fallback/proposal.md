## Why

`/tdd` hiện bị block khi không detect được framework test, dù ở nhiều change giai
đoạn đầu team chỉ cần behavioral scenarios để làm done criteria cho `/code`. Điều
này làm skill mất tác dụng đúng ở lúc repo chưa có stack kiểm thử rõ ràng.

## What Changes

- **MODIFIED**: `/tdd` chuyển sang hỗ trợ hai mode output:
  - runnable test files khi detect được framework rõ ràng
  - scenario-based test specs khi không detect được framework
- Khi không detect được framework, `/tdd` mặc định generate scenario files trong
  `openspec/changes/<name>/tests/` thay vì block và hỏi ngay
- Chỉ hỏi human khi framework detection mơ hồ hoặc khi user yêu cầu runnable tests
- Chuẩn hóa format scenario-based tests để `/code` vẫn dùng được như implementation context

## Capabilities

### New Capabilities

### Modified Capabilities
- `tdd-skill`: Mở rộng output generation để fallback sang scenario-based tests khi không detect được framework test

## Impact

- `.codex/skills/tdd/SKILL.md` - sửa flow framework detection và output fallback
- `openspec/specs/tdd-skill/spec.md` - cập nhật requirements cho dual-mode generation
- `docs/team-ai-workflow.md` - cập nhật định nghĩa output của `/tdd` nếu workflow docs cần phản ánh scenario mode

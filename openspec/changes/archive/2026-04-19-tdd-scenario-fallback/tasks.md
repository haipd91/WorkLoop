## 1. Cập nhật contract của /tdd

- [x] 1.1 Sửa `openspec/specs/tdd-skill/spec.md` để mô tả dual-mode output: runnable mode và scenario mode
- [x] 1.2 Làm rõ rule: chỉ hỏi human khi framework detection mơ hồ hoặc khi user yêu cầu runnable tests
- [x] 1.3 Bổ sung requirement cho scenario-based fallback trong `tests/`

## 2. Cập nhật skill /tdd

- [x] 2.1 Sửa `.codex/skills/tdd/SKILL.md` để fallback sang scenario-based tests khi không detect được framework
- [x] 2.2 Chuẩn hóa format scenario files để mỗi file gắn được với task hoặc capability và có happy path, edge cases, error cases
- [x] 2.3 Giữ review loop hiện tại để human vẫn approve trước khi handoff sang `/code`

## 3. Cập nhật docs và kiểm tra workflow

- [x] 3.1 Cập nhật `docs/team-ai-workflow.md` nếu tài liệu cần phản ánh scenario mode là output hợp lệ của `/tdd`
- [x] 3.2 Rà lại interaction giữa `/tdd` và `/code` để đảm bảo `tests/` vẫn là contract duy nhất cần giữ
- [x] 3.3 Kiểm tra một case không có framework và một case có framework để xác nhận behavior mong muốn đã rõ trong artifacts

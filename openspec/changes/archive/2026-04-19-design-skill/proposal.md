## Why

Workflow hiện tại thiếu một bước phân tích kỹ thuật có cấu trúc trước khi `/code`
bắt đầu. Điều này làm team phát hiện rủi ro kiến trúc và impact quá muộn, khi AI
đã đi vào implementation.

## What Changes

- **ADDED**: Skill `/design` mới để tạo `design.md` từ artifact của change hiện tại
- Skill đọc `proposal.md` và `tasks.md`, tận dụng `brief.md` nếu có, rồi tự xây bộ
  criteria phân tích impact theo từng nhóm chuyên biệt
- Skill chạy parallel agents theo từng nhóm criteria, tổng hợp kết quả thành một
  bản design hợp nhất với kiến trúc chi tiết, impact map, assumptions và trade-offs
- Skill hỗ trợ human review và feedback loop trước khi chốt `design.md`
- Design đã approve trở thành input chính thức cho `/code`

## Capabilities

### New Capabilities
- `design-skill`: Tạo và lặp lại `design.md` từ artifacts của một change, dùng
  phân tích song song theo nhóm criteria và review gate trước khi handoff sang `/code`

### Modified Capabilities

## Impact

- `.codex/skills/design/SKILL.md` - skill mới cho flow phân tích kỹ thuật trước implementation
- `docs/team-ai-workflow.md` - cập nhật workflow để chèn `/design` giữa `/propose` và `/code`
- `openspec/changes/<name>/design.md` - artifact được tạo hoặc cập nhật sau mỗi vòng review

# Test Scenario: 1 design skill setup

- Source tasks:
  - 1.1 Tạo `.codex/skills/design/SKILL.md` với frontmatter, mô tả input và workflow chọn change
  - 1.2 Implement bước đọc artifacts của change: `proposal.md`, `tasks.md`, và `brief.md` nếu tồn tại
  - 1.3 Định nghĩa format output chuẩn cho `design.md` để skill luôn sinh tài liệu reviewable
- Related requirements:
  - Generate design artifact from change artifacts
  - Synthesize a standardized design document
- Assumptions:
  - Change directory là source of truth cho `/design`
  - `proposal.md` và `tasks.md` là input tối thiểu để bắt đầu

## Happy Path

- Khi user chạy `/design` với một change có `proposal.md` và `tasks.md`
- Thì skill chọn đúng change, đọc đủ artifacts cần thiết, và khởi tạo draft `design.md` theo format chuẩn
- Và nếu `brief.md` tồn tại thì skill dùng nó làm context bổ sung thay vì bỏ qua

## Edge Cases

- `brief.md` không tồn tại nhưng `proposal.md` và `tasks.md` có đủ
- Change có thêm artifacts khác không liên quan, nhưng skill vẫn ưu tiên source files đã định nghĩa
- `design.md` đã tồn tại từ vòng trước, skill cập nhật tiếp thay vì làm lệch format

## Error Cases

- `proposal.md` bị thiếu nên skill không xác định được mục tiêu thay đổi
- `tasks.md` bị thiếu nên skill không suy ra được phạm vi implementation
- User chỉ đưa tên change không tồn tại trong `openspec/changes/`

## Expected Outcomes

- Skill báo rõ file nào là required input và file nào chỉ là optional context
- Skill chỉ bắt đầu generate khi có đủ input tối thiểu
- Draft `design.md` luôn bám một cấu trúc ổn định, dễ review

## Notes for Implementation

- Không quét toàn repo để thay thế input từ change directory
- Nếu thiếu input tối thiểu, lỗi phải nêu cụ thể file còn thiếu thay vì báo chung chung

# Test Scenario: 4 design workflow integration

- Source tasks:
  - 4.1 Cập nhật tài liệu workflow để thêm `/design` giữa `/propose` và `/code`
  - 4.2 Nêu rõ trong tài liệu rằng `design.md` là input chính thức cho `/code`
  - 4.3 Kiểm tra lại flow end-to-end: `/propose` -> `/design` -> `/code`
- Related requirements:
  - Preserve approved design as `/code` input
  - Generate design artifact from change artifacts
- Assumptions:
  - `/design` là bước trung gian bắt buộc theo workflow mới trước implementation
  - `/code` đọc `design.md` như implementation context chứ không tự tạo lại design

## Happy Path

- Khi workflow docs đã được cập nhật
- Thì team thấy rõ flow `/propose` -> `/design` -> `/code`
- Và `design.md` được coi là artifact chính thức để `/code` đọc trước khi implement
- `/design` kết thúc ở trạng thái review-complete nhưng không tự động trigger `/code`

## Edge Cases

- Workflow cũ vẫn còn mention `/propose` sang `/code` trực tiếp ở vài chỗ và cần được rà lại
- Một change đã có `design.md` từ trước nhưng chưa approve, `/code` không nên coi đó là handoff hoàn chỉnh
- Tài liệu mô tả `/design` là optional trong một số context, cần làm rõ rule áp dụng

## Error Cases

- Docs vẫn để flow cũ khiến human bỏ qua `/design`
- `design.md` được tạo nhưng không được nhắc như input chính thức cho `/code`
- `/design` tự động gọi `/code` làm mất review gate

## Expected Outcomes

- Workflow docs nhất quán với capability mới của `/design`
- Team hiểu rõ `design.md` là output đã approve và là input cho implementation
- Không có automation ngầm chuyển từ `/design` sang `/code`

## Notes for Implementation

- Rà cả workflow docs lẫn skill text để tránh message mâu thuẫn
- Nếu có exception cho một số loại change, phải nêu rõ thay vì để implicit

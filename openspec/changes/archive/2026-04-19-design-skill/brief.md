# Brief: Design Skill

## Problem
Trong giai đoạn coding, team có nguy cơ phát triển sai kiến trúc và bỏ sót
phạm vi ảnh hưởng. Hiện tại chưa có bước phân tích kỹ thuật rõ ràng với
human review gate trước khi implementation bắt đầu.

## Goal
Team có thể đọc design artifact và biết trước AI sẽ implement như thế nào,
từ đó phát hiện vấn đề kiến trúc và impact sớm — trước khi code chạy, không
phải sau.

## Scope

### In scope
- Tạo skill `/design` mới
- Skill đọc output của `/propose` (proposal.md, tasks.md) làm input
- Tự xây dựng criteria phân tích impact, group thành các nhóm chuyên biệt
- Chạy parallel agents, mỗi agent phụ trách một nhóm criteria
- Tổng hợp kết quả thành `design.md` với kiến trúc chi tiết và impact map
- Hỗ trợ human review và feedback loop để chỉnh sửa trước khi lưu
- Output của `/design` là input của `/code`

### Out of scope
- Không thay đổi logic bên trong `/propose` hoặc `/code`
- Không tự động chạy `/code` sau khi design xong

## Constraints
- Format `design.md` do Claude thiết kế để đảm bảo đầy đủ và chuẩn hóa
- Parallel agents phải chạy đồng thời để tiết kiệm thời gian

## Acceptance Criteria
- [ ] Chạy `/design` tạo ra `design.md` với đầy đủ nội dung có thể có:
      kiến trúc chi tiết, impact theo từng nhóm criteria
- [ ] Human có thể review và feedback, skill hỗ trợ chỉnh sửa iterative
- [ ] Team có thể approve design và chuyển sang `/code` mà không cần
      hỏi thêm thông tin

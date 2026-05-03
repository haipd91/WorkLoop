# Brief: TDD Skill

## Problem
Bước `/code` implement trực tiếp từ tasks mà chưa có test nào được định nghĩa
trước. AI không có "done criteria" rõ ràng — không biết implement đúng hay sai
cho đến khi chạy xong.

## Goal
Team có file test sẵn trước khi `/code` chạy. AI implement đúng vào test cases
đã định nghĩa, human biết trước kỳ vọng của từng task.

## Scope

### In scope
- Tạo skill `/tdd` mới, chạy độc lập do human trigger
- Đọc toàn bộ artifacts làm input: proposal.md, specs/, design.md, tasks.md
- Generate test files vào `openspec/changes/<name>/tests/`
- Generate test cho tất cả cases có thể xác định được
- Human review và approve test files trước khi chuyển sang `/code`
- Sửa skill `/code` để tự động đọc `tests/` nếu có làm input

### Out of scope
- Không chạy hay verify tests
- Không tự động trigger `/code` sau khi generate xong

## Constraints
- AI tự detect framework và format test theo project (không hardcode)
- `/code` cần được update để nhận `tests/` làm context bổ sung

## Acceptance Criteria
- [ ] `/tdd` generate đủ test files vào `openspec/changes/<name>/tests/`
      cho tất cả cases xác định được từ artifacts
- [ ] Human có thể review, feedback và approve trước khi chuyển sang `/code`
- [ ] `/code` tự động đọc `tests/` nếu thư mục tồn tại

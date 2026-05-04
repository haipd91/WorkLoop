# Brief: Spec Context Discovery Propose

## Problem

`/propose` hiện có thể tạo proposal/spec/design/tasks từ request hiện tại mà không chắc đã tìm và đọc các spec cũ liên quan đến cùng domain, màn hình, action hoặc capability.

Ví dụ: request "bổ sung validate cho màn hình login" cần đọc spec liên quan đến login/auth/validation trước khi quyết định tạo capability mới hay sửa capability cũ.

Nếu thiếu bước này, workflow có thể tạo requirement trùng, chọn `ADDED` sai thay vì `MODIFIED`, bỏ sót constraint cũ, hoặc conflict với active change khác.

## Goal

Thêm bước Spec Context Discovery nhẹ vào `/propose` để mọi change mới được tạo dựa trên context OpenSpec hiện có trước khi sinh proposal/spec/design/tasks.

## Scope

### In scope

- Cập nhật `/propose` để extract keyword từ user request và `brief.md` nếu có.
- Search active specs trong `openspec/specs/**`.
- Search active changes trong `openspec/changes/**`, loại trừ `archive/**`.
- Đọc matched specs/active change artifacts trước khi phân loại capability.
- Dùng context tìm được để quyết định new vs existing capability.
- Dùng context tìm được để chọn `ADDED`, `MODIFIED`, `REMOVED`, hoặc `RENAMED`.
- Ghi related specs và overlapping active changes vào artifact một cách ngắn gọn, reviewable.
- Chỉ search archive khi active context thiếu, conflict, hoặc cần rationale.

### Out of scope

- Không thêm vector DB hoặc embedding infra ở bước này.
- Không bắt buộc search toàn bộ archive cho mọi proposal.
- Không sửa application code nếu proposal sau này không chỉ ra nhu cầu rõ ràng.

## Constraints

- Ưu tiên workflow nhẹ, dùng keyword/spec discovery trước, chưa dùng semantic infra nặng.
- Active specs là source of truth chính.
- Active changes cần được kiểm tra để tránh conflict.
- Archive chỉ là lịch sử/rationale, không phải context mặc định.
- Thay đổi nên tập trung vào workflow/spec/skill artifacts.

## Acceptance Criteria

- [ ] `/propose` thực hiện Spec Context Discovery trước khi tạo hoặc cập nhật `proposal.md`, `specs/**/*.md`, `design.md`, và `tasks.md`.
- [ ] Discovery search active specs bằng terms extract từ request/brief.
- [ ] Discovery search active changes và loại trừ archived changes.
- [ ] Matched specs được đọc trước khi phân loại capability.
- [ ] Proposal generation phân biệt existing vs new capabilities dựa trên discovered context.
- [ ] Spec generation dùng discovered context để chọn đúng `ADDED`, `MODIFIED`, `REMOVED`, hoặc `RENAMED`.
- [ ] Generated artifacts ghi related specs và overlapping active changes ngắn gọn, reviewable.
- [ ] `/propose` hỏi clarification khi capability mapping hoặc active-change overlap mơ hồ.
- [ ] Archive search là optional, chỉ dùng khi cần conflict/rationale gap.

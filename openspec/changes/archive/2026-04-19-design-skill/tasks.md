## 1. Tạo skill /design

- [x] 1.1 Tạo `.codex/skills/design/SKILL.md` với frontmatter, mô tả input và workflow chọn change
- [x] 1.2 Implement bước đọc artifacts của change: `proposal.md`, `tasks.md`, và `brief.md` nếu tồn tại
- [x] 1.3 Định nghĩa format output chuẩn cho `design.md` để skill luôn sinh tài liệu reviewable

## 2. Implement pipeline phân tích design

- [x] 2.1 Implement bước sinh criteria groups từ change context để bao phủ kiến trúc, impact và rủi ro
- [x] 2.2 Implement chạy parallel agents, mỗi agent phụ trách một nhóm criteria độc lập
- [x] 2.3 Implement bước tổng hợp kết quả agent thành một draft `design.md` thống nhất
- [x] 2.4 Đảm bảo draft ghi rõ assumptions, trade-offs và open questions khi context chưa đủ

## 3. Implement review loop

- [x] 3.1 Hiển thị summary ngắn của design draft để human review nhanh
- [x] 3.2 Implement feedback loop để human yêu cầu chỉnh sửa và skill cập nhật đúng section liên quan
- [x] 3.3 Chỉ xác nhận hoàn tất khi design đã được human approve

## 4. Tích hợp workflow

- [x] 4.1 Cập nhật tài liệu workflow để thêm `/design` giữa `/propose` và `/code`
- [x] 4.2 Nêu rõ trong tài liệu rằng `design.md` là input chính thức cho `/code`
- [x] 4.3 Kiểm tra lại flow end-to-end: `/propose` -> `/design` -> `/code`

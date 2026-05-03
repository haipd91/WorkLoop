# Tasks: brief-skill

## T1 — Tạo thư mục và file SKILL.md ✓

- Tạo `.claude/skills/brief/SKILL.md`
- Viết frontmatter: name, description, license, compatibility
- Không viết nội dung instruction ở task này

**Done khi:** file tồn tại với frontmatter đúng format

---

## T2 — Viết instruction cho skill ✓

Viết nội dung SKILL.md với đầy đủ:

- Mô tả skill làm gì
- Luồng hỏi 3 round (problem+goal → scope → constraints+AC)
- Logic adaptive clarify (khi nào hỏi thêm, khi nào không)
- Brief format output (dùng template cố định từ design.md)
- Logic lưu file (path mặc định, xử lý file đã tồn tại)
- Câu hỏi approve cuối (dùng AskUserQuestion)
- Gợi ý `/opsx:propose` sau khi lưu

**Done khi:** đọc SKILL.md có thể hiểu và follow đúng flow trong design.md

---

## T3 — Test chạy thực tế

Chạy `/brief` trong Claude Code với 2 scenario:

1. **Happy path**: trả lời rõ ràng → brief.md tạo đúng 5 thành phần
2. **Mơ hồ path**: trả lời vague ở 1-2 chỗ → Claude clarify đúng → brief.md vẫn đầy đủ

Kiểm tra:
- Brief output đúng format
- File được lưu đúng path
- Gợi ý `/opsx:propose` xuất hiện sau khi lưu

**Done khi:** cả 2 scenario pass, brief.md có thể feed vào `/opsx:propose` không cần chỉnh tay

---

## T4 — Cập nhật team-ai-workflow.md ✓

Cập nhật section 4.1 Intake và 4.2 Clarification:
- Ghi nhận `/brief` là tool chính cho 2 stage này
- Cập nhật Visual Workflow (section 5) để phản ánh `/brief`
- Cập nhật section 6 Mapping Công Cụ

**Done khi:** workflow doc nhất quán với skill mới

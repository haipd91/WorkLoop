# Proposal: brief-skill

## Problem

Team đang xây dựng workflow AI-native trong đó AI thực thi và human review. Điểm vào của toàn bộ flow là `brief.md` — nhưng hiện tại không có cơ chế chuẩn hóa để tạo brief. Mỗi người tự viết theo cách riêng hoặc bỏ qua bước này, dẫn đến spec và plan phía sau bị lệch hướng.

## Solution

Tạo skill `/brief` cho Claude Code. Skill này đóng vai trò interviewer: hỏi human từng bước theo cách adaptive và conversational, thu thập đủ 5 nhóm thông tin cần thiết, sau đó tạo `brief.md` draft để human approve.

Skill này thay thế hai stage `Intake` và `Clarification` trong team workflow, gộp lại thành một bước duy nhất có AI dẫn dắt.

## Goals

- Human không cần biết format brief — chỉ cần trả lời câu hỏi
- Brief output đủ chất lượng để `/opsx:propose` tạo spec tốt
- Flow ngắn, không hỏi dư, không gây mệt mỏi
- Human giữ quyền approve trước khi brief được lưu

## Non-goals

- Không tự động chạy `/opsx:propose` sau khi brief xong
- Không xử lý brief cho project lớn hoặc multi-feature
- Không thay thế spec, plan hay bất kỳ artifact nào khác

## Success Criteria

- Chạy `/brief` → trả lời câu hỏi → có `brief.md` được human approve trong dưới 10 phút
- Brief đủ 5 thành phần: problem, goal, scope, constraints, acceptance criteria
- `/opsx:propose` đọc được brief và tạo spec không cần clarify thêm

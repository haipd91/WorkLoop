## 1. Sửa contract của design-skill

- [x] 1.1 Cập nhật spec của `design-skill` để thay “parallel analysis” mơ hồ bằng mô hình orchestrator + specialist agents
- [x] 1.2 Bổ sung requirement mô tả ownership, contract và conflict resolution của specialist agents
- [x] 1.3 Làm rõ `agent-as-tool` là collaboration pattern mặc định, còn handoff chỉ dùng khi cần delegated ownership

## 2. Sửa skill /design

- [x] 2.1 Sửa `.codex/skills/design/SKILL.md` để giới thiệu orchestrator agent là điểm điều phối trung tâm
- [x] 2.2 Định nghĩa bộ specialist agents mặc định và rule bật thêm optional agents theo context
- [x] 2.3 Chuẩn hóa input/output schema cho từng specialist agent để tránh overlap
- [x] 2.4 Bổ sung bước synthesis rõ ràng: merge outputs, xử lý conflict, ghi `design.md`

## 3. Materialize specialist agents

- [x] 3.1 Tạo `.codex/agents/` và tách core specialist agents thành các file definition riêng
- [x] 3.2 Tách ít nhất các agent: architecture, impact, failure-modes, data-flow, review-docs
- [x] 3.3 Cập nhật `.codex/skills/design/SKILL.md` để tham chiếu `.codex/agents/` thay vì chỉ mô tả agent nội tuyến

## 4. Cập nhật workflow và tài liệu

- [x] 4.1 Cập nhật `docs/team-ai-workflow.md` để phản ánh `/design` là bước orchestration nhiều specialist agents
- [x] 4.2 Làm rõ relationship giữa `/design`, `/tdd`, và `/code` sau khi thêm orchestrator model
- [x] 4.3 Thêm guidance ngắn trong docs về khi nào dùng handoff và khi nào dùng manager-controlled sub-agents

## 5. Kiểm tra logic end-to-end

- [x] 5.1 Kiểm tra case change nhỏ chỉ cần core agents
- [x] 5.2 Kiểm tra case change phức tạp cần bật thêm optional agents
- [x] 5.3 Kiểm tra case outputs mâu thuẫn để bảo đảm conflict được surfaced thay vì bị ẩn

# Test Scenario: 3 archive-gate-updates

- Source tasks:
  - 3.1 Update `/archive` guidance to require durable PR or merge evidence for GitHub-backed changes.
  - 3.2 Define how archive blocks when review passes but PR/merge evidence is missing.
  - 3.3 Preserve existing `review.md` metadata, stale diff, and blocking findings checks.
  - 3.4 Document local-only or legacy bypass handling with residual risk.
- Related requirements:
  - Require GitHub delivery evidence before archive
  - Preserve human archive acceptance
  - Preserve phase 1 review metadata gate
  - Block archive on stale review
- Assumptions:
  - Archive is final enforcement point.
  - GitHub-backed archive requires both local review evidence and durable GitHub evidence.

## Happy Path

- Khi `/archive` chạy cho GitHub-backed change có `review.md` pass, no blocking findings, current reviewed diff, và PR/merge evidence
- Thì archive tiếp tục các checks còn lại.
- Thì summary trước archive hiển thị review verdict, residual risk, PR link hoặc merge record, và issue linkage.

- Khi `/archive` chạy cho local-only change có explicit human acceptance
- Thì archive hiển thị bypass reason và residual risk trước final action.

## Edge Cases

- Khi review graph state là done nhưng `review.md` metadata malformed
- Thì archive vẫn block theo metadata gate cũ.

- Khi PR exists nhưng merge state không rõ
- Thì archive block hoặc yêu cầu `/github:merge <pr>`, không tự suy đoán.

- Khi legacy archived-style work không có GitHub evidence
- Thì archive chỉ cho qua nếu bypass được document rõ.

## Error Cases

- Khi `review.md` pass nhưng không có PR/merge evidence
- Thì archive phải block.

- Khi current diff khác `reviewed_diff`
- Thì archive phải block và yêu cầu rerun `/review`, kể cả GitHub evidence tồn tại.

- Khi archive chỉ check `review.md` và không nhắc GitHub evidence cho GitHub-backed work
- Thì verification phải fail.

## Expected Outcomes

- Archive không còn là đường bypass GitHub workflow.
- Review metadata gate không bị thay thế hoặc làm yếu.
- Human thấy rõ risk trước final archive.

## Notes for Implementation

- Cần quyết định minimal evidence source cho implementation đầu tiên: PR URL/path trong artifact/handoff, local git branch/remote, hoặc GitHub MCP/plugin.
- Nếu chưa thể tự verify GitHub state, block và hỏi PR/merge evidence.

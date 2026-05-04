# Test Scenario: 4 verification-and-regression

- Source tasks:
  - 4.1 Run OpenSpec validation for `strengthen-github-workflow-handoffs`.
  - 4.2 Search active skills and schema guidance for direct `/review` pass to `/archive` bypasses.
  - 4.3 Verify GitHub orchestration skills still own GitHub operations and core skills only hand off.
  - 4.4 Record any remaining ambiguity around GitHub-backed detection as residual risk for `/review`.
- Related requirements:
  - Handoff contract
  - MCP GitHub usage
  - Require GitHub delivery evidence before archive
  - Local-only exception
- Assumptions:
  - This change is mostly workflow/spec/skill instruction work.
  - Verification is text/search/validation heavy rather than runnable unit tests.

## Happy Path

- Khi `openspec validate strengthen-github-workflow-handoffs` chạy
- Thì validation pass.

- Khi search active `.codex/skills` và `openspec/schemas` cho direct archive guidance
- Thì không có default handoff nào route GitHub-backed work từ `/review` pass thẳng `/archive`.

- Khi review GitHub orchestration skills
- Thì GitHub read/write operations vẫn nằm trong `/github:*` skills.
- Thì core skills chỉ hand off và nêu blockers.

## Edge Cases

- Khi text `/archive <name>` vẫn tồn tại trong archive skill hoặc merge skill
- Thì chỉ fail nếu nó là default bypass trước GitHub merge, không fail nếu là post-merge handoff hợp lệ.

- Khi local-only exception text xuất hiện
- Thì phải kèm explicit human acceptance và residual risk.

## Error Cases

- Khi validation fail
- Thì implementation chưa hoàn tất.

- Khi active skill guidance còn câu `Review passed. Run /archive <name> when ready` mà không phân biệt GitHub-backed
- Thì verification phải fail.

- Khi core skill tự yêu cầu token hoặc tự gọi custom GitHub HTTP client
- Thì verification phải fail vì vi phạm GitHub orchestration boundary.

## Expected Outcomes

- Change có validation pass.
- Không còn bypass mặc định cho GitHub-backed work.
- Residual risk ghi rõ phần chưa tự động detect được GitHub-backed/local-only.

## Notes for Implementation

- Dùng `rg` để kiểm text bypass.
- Không cần tạo GitHub Actions hoặc issue/PR template trong change này.

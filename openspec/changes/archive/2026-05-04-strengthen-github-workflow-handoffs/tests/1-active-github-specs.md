# Test Scenario: 1 active-github-specs

- Source tasks:
  - 1.1 Sync `github-workflow` into active `openspec/specs/github-workflow/spec.md`.
  - 1.2 Sync `github-orchestration-skills` into active `openspec/specs/github-orchestration-skills/spec.md`.
  - 1.3 Verify active specs describe `/explore -> /github:create-issue -> OpenSpec -> GitHub PR/review/merge -> /archive`.
- Related requirements:
  - GitHub source of truth
  - Issue intake lifecycle
  - OpenSpec artifact ownership
  - End-to-end GitHub handoff order
- Assumptions:
  - Active specs under `openspec/specs/` are the durable source after archive.
  - Archived change specs are useful source material but not active requirements.

## Happy Path

- Khi implementation hoàn tất sync specs
- Thì `openspec/specs/github-workflow/spec.md` tồn tại và mô tả GitHub là durable source of truth.
- Thì `openspec/specs/github-orchestration-skills/spec.md` tồn tại và mô tả skill set GitHub.
- Thì active specs có flow chuẩn: `/explore -> /github:create-issue -> /brief|/propose -> /tdd -> /apply -> /github:create-pr -> /review -> /github:post-review -> /github:sync-review -> /github:address-comments nếu cần -> /github:merge -> /archive`.

## Edge Cases

- Khi archived GitHub specs và change delta specs khác nhau
- Thì active specs phải theo delta specs của change hiện tại, không copy mù từ archive.

- Khi local-only exception được mô tả
- Thì exception phải yêu cầu human acceptance và residual risk, không biến GitHub workflow thành optional mặc định.

## Error Cases

- Khi `github-workflow` hoặc `github-orchestration-skills` chỉ tồn tại trong `openspec/changes/archive/`
- Thì verification phải fail vì active specs chưa có source-of-truth contract.

- Khi active specs thiếu `/explore` hoặc `/github:create-issue` trong flow chuẩn
- Thì verification phải fail vì discovery-to-issue handoff chưa được capture.

## Expected Outcomes

- Active specs chứa GitHub workflow contract.
- Active specs không làm suy yếu OpenSpec review gate hiện có.
- GitHub-backed default và local-only exception được phân biệt rõ.

## Notes for Implementation

- Ưu tiên tạo spec active mới từ delta specs trong change.
- Sau sync, chạy OpenSpec validation cho change.

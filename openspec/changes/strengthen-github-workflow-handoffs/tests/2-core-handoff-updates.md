# Test Scenario: 2 core-handoff-updates

- Source tasks:
  - 2.1 Update `/apply` handoff so GitHub-backed completed implementation routes to `/github:create-pr <change-name>` before `/review`.
  - 2.2 Update `/review` pass handoff so GitHub-backed changes route to `/github:post-review <pr>` and `/github:sync-review <pr>` before merge/archive.
  - 2.3 Update schema apply done instruction so generated OpenSpec apply guidance matches the GitHub-aware handoff.
  - 2.4 Preserve explicit local-only exception language for changes where the human accepts skipping GitHub.
- Related requirements:
  - Hand off to /review after implementation
  - Core skills hand off to orchestration skills
  - End-to-end GitHub handoff order
- Assumptions:
  - Core OpenSpec skills should hand off to GitHub skills, not implement all GitHub operations themselves.
  - GitHub-backed is default unless local-only is explicitly accepted.

## Happy Path

- Khi `/apply` hoàn tất tasks cho GitHub-backed change
- Thì output handoff đề xuất `/github:create-pr <change-name>` trước `/review`.
- Thì output không nói change đã sẵn sàng archive.

- Khi `/review` pass cho GitHub-backed change
- Thì output handoff đề xuất `/github:post-review <pr>` và `/github:sync-review <pr>`.
- Thì output không đề xuất `/archive <name>` trực tiếp.

- Khi `openspec instructions apply --change <name> --json` trả state all_done
- Thì `doneInstruction` route GitHub-backed work sang `/github:create-pr <name>` thay vì archive.

## Edge Cases

- Khi change được human đánh dấu local-only
- Thì `/apply` có thể hand off `/review <change-name>`, nhưng phải ghi rõ GitHub issue/PR evidence đã được skip có chủ ý.

- Khi PR target chưa rõ sau `/review`
- Thì handoff phải yêu cầu PR number/URL hoặc `/github:create-pr`, không đoán.

## Error Cases

- Khi `/apply` all_done vẫn chỉ nói `Run /review before archive`
- Thì verification phải fail vì thiếu GitHub PR handoff.

- Khi `/review` pass vẫn nói `Run /archive when ready`
- Thì verification phải fail vì bỏ qua post-review, sync-review, merge.

## Expected Outcomes

- Core skills đóng vai trò điều hướng workflow.
- GitHub operations vẫn thuộc GitHub orchestration skills.
- Không còn đường mặc định `/apply -> /review -> /archive` cho GitHub-backed changes.

## Notes for Implementation

- Kiểm tra `.codex/skills/apply/SKILL.md`, `.codex/skills/review/SKILL.md`, và `openspec/schemas/spec-driven-custom/schema.yaml`.
- Search active files sau sửa để tìm bypass text còn sót.

# Test Scenario: 2 propose workflow guidance

- Source tasks:
  - 2.1 Update `.codex/skills/propose/SKILL.md` to run Spec Context Discovery before artifact generation.
  - 2.2 Define request/brief term extraction for domain, action, entity, UI surface, and likely capability terms.
  - 2.3 Define active spec search and matched-spec read behavior before capability classification.
  - 2.4 Define active change overlap search while excluding archived changes by default.
  - 2.5 Define clarification behavior when capability mapping or active-change overlap is ambiguous.
  - 2.6 Define archive search as optional and rationale-driven only.
- Related requirements:
  - Discover existing spec context before proposal generation
  - Detect overlapping active changes
  - Limit archive search to rationale gaps
- Assumptions:
  - `/propose` remains instruction-driven; no new runtime command wrapper is required.

## Happy Path

- Khi `/propose <change-name>` chạy với request hoặc `brief.md`.
- Thì skill guidance yêu cầu extract terms từ domain, action, entity, surface, error/validation/security terms, và likely capability names.
- Thì guidance yêu cầu search `openspec/specs/**` trước capability classification.
- Thì matched specs được đọc trước khi viết `proposal.md` hoặc delta spec.
- Thì guidance yêu cầu search active changes trong `openspec/changes/**` và exclude `openspec/changes/archive/**`.
- Thì artifact generation chỉ tiếp tục sau discovery.

## Edge Cases

- Nếu nhiều capability match ngang nhau.
- Thì `/propose` phải hỏi clarification thay vì tự chọn.
- Nếu active change khác overlap cùng capability/domain.
- Thì `/propose` phải hỏi continue existing, merge scope, hoặc proceed new change.
- Nếu active specs đủ rõ.
- Thì archive search không chạy mặc định.

## Error Cases

- Nếu guidance vẫn cho phép tạo proposal trước rồi mới search specs.
- Thì scenario fail.
- Nếu guidance search toàn `openspec/changes/**` mà không exclude archive.
- Thì scenario fail.
- Nếu archive search là bước mặc định.
- Thì scenario fail.

## Expected Outcomes

- `/propose` có pre-artifact discovery step rõ ràng.
- Context discovery không phụ thuộc chat memory.
- Active spec và active change context quyết định new vs modified capability.

## Notes for Implementation

- Không hardcode một command duy nhất nếu wording vẫn bắt buộc đúng paths/exclusions/read-before-write.

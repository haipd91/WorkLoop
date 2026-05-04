# Test Scenario: 3 schema instruction alignment

- Source tasks:
  - 3.1 Update `openspec/schemas/spec-driven-custom/schema.yaml` proposal instructions to require discovery before capability selection.
  - 3.2 Update specs instructions to require using discovered context for `ADDED`, `MODIFIED`, `REMOVED`, and `RENAMED` choices.
  - 3.3 Keep schema and skill wording aligned so `openspec instructions proposal/specs` does not weaken `/propose` guidance.
- Related requirements:
  - Discover existing spec context before proposal generation
  - Record discovered context in planning artifacts
- Assumptions:
  - `openspec instructions proposal --change <name> --json` and `openspec instructions specs --change <name> --json` are used by `/propose`.

## Happy Path

- Khi chạy `openspec instructions proposal --change issue-7-spec-context-discovery-propose --json` sau implementation.
- Thì instruction nói rõ phải discover active specs và active changes trước capability selection.
- Khi chạy `openspec instructions specs --change issue-7-spec-context-discovery-propose --json`.
- Thì instruction nói rõ phải dùng discovered context để chọn `ADDED`, `MODIFIED`, `REMOVED`, hoặc `RENAMED`.
- Thì schema instruction không mâu thuẫn với `.codex/skills/propose/SKILL.md`.

## Edge Cases

- Nếu schema chỉ nói "Research existing specs" chung chung nhưng skill có discovery chi tiết.
- Thì scenario nên fail vì schema vẫn quá yếu và dễ drift.
- Nếu proposal instruction có discovery nhưng specs instruction không nhắc delta operation dựa trên discovered context.
- Thì scenario fail vì spec generation vẫn có thể chọn sai delta operation.

## Error Cases

- Nếu `openspec instructions proposal` bỏ qua active change overlap.
- Thì scenario fail.
- Nếu schema instruction bắt archive search mặc định.
- Thì scenario fail.
- Nếu schema instruction nhắc embedding/vector DB.
- Thì scenario fail vì ngoài scope.

## Expected Outcomes

- Skill và schema cùng mô tả một contract.
- CLI-generated instructions không làm yếu behavior của `/propose`.
- Proposal/spec instructions vẫn ngắn, không biến schema thành tutorial dài.

## Notes for Implementation

- Verify bằng chính output JSON của `openspec instructions`, không chỉ đọc YAML trực tiếp.

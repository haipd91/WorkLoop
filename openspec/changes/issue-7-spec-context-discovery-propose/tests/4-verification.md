# Test Scenario: 4 verification

- Source tasks:
  - 4.1 Run OpenSpec validation for `issue-7-spec-context-discovery-propose`.
  - 4.2 Search active workflow instructions for `/propose` paths that create artifacts without discovery.
  - 4.3 Verify archive search is not required by default in active guidance.
  - 4.4 Verify generated artifact guidance remains concise and does not introduce embedding/vector dependencies.
- Related requirements:
  - Detect overlapping active changes
  - Limit archive search to rationale gaps
  - Record discovered context in planning artifacts
- Assumptions:
  - Verification is documentation/spec-oriented because this change is workflow-instruction heavy.

## Happy Path

- Khi implementation xong.
- Thì `openspec validate --changes issue-7-spec-context-discovery-propose` pass.
- Thì search active guidance cho `/propose` không còn path tạo proposal/spec/design/tasks mà thiếu discovery step.
- Thì active guidance nói archive search chỉ optional khi active context thiếu, conflict, hoặc cần rationale.
- Thì search active guidance không thấy yêu cầu embedding, vector DB, hoặc semantic infrastructure.

## Edge Cases

- Nếu archived historical files vẫn nhắc old propose behavior.
- Thì không fail nếu active guidance đã đúng và archive không được xem là active source.
- Nếu một doc active nhắc `/propose` ở mức lifecycle nhưng không mô tả artifact generation.
- Thì không fail nếu doc đó không phải instruction path tạo artifact.

## Error Cases

- Nếu `.codex/skills/propose/SKILL.md` vẫn cho tạo planning artifacts trực tiếp sau brief/status mà không discovery.
- Thì scenario fail.
- Nếu `openspec/schemas/spec-driven-custom/schema.yaml` vẫn chỉ có weak guidance không bắt discovery.
- Thì scenario fail.
- Nếu proposal template yêu cầu evidence dài hoặc full copied spec content.
- Thì scenario fail.

## Expected Outcomes

- Change validate sạch.
- Active propose workflow có discovery gate rõ.
- Archive search không trở thành default.
- Không thêm dependency hoặc infra ngoài scope.

## Notes for Implementation

- Search active paths trước: `.codex/skills/`, `openspec/schemas/`, `openspec/specs/`.
- Phân biệt active guidance với `openspec/changes/archive/**`.

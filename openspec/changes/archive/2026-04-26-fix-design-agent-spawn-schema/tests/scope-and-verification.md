# Test Scenario: scope-and-verification

- Source tasks:
  - 2.3 Confirm this change does not modify the agent registry, `DesignAnalysisResult` schema, application code, or runtime tooling.
  - 3.1 Run OpenSpec validation for `fix-design-agent-spawn-schema`.
  - 3.2 Review the final diff to confirm the change is limited to docs/spec/test planning artifacts and `/design` skill documentation.
  - 3.3 Confirm `/tdd fix-design-agent-spawn-schema` has enough acceptance criteria to create a scenario for the rejected spawn pattern.
- Related requirements:
  - `design-skill`: Specialist spawn uses schema-safe Codex calls
  - `design-agent-contracts`: Codex spawn schema constraints are explicit
- Assumptions:
  - This change should remain small and documentation-focused.
  - Scenario tests are the correct output mode because no runnable test framework is detected.

## Happy Path

- Khi implementation hoàn tất
- Thì changed files chỉ gồm `/design` skill documentation, OpenSpec specs/artifacts/tests cho change này, và không có app runtime code.
- Thì `openspec validate fix-design-agent-spawn-schema --strict` pass.
- Thì task checklist có thể được marked done dựa trên concrete file review.

## Edge Cases

- Nếu existing worktree có unrelated dirty files, implementation không được revert hoặc chỉnh các file đó.
- Nếu docs cần nhắc `spawn_agent`, wording phải mô tả runtime guidance, không giả vờ có automatic validation.
- Nếu OpenSpec status vẫn thiếu tests trước khi `/tdd`, sau khi scenario files được tạo status phải unlock apply prerequisites theo schema.

## Error Cases

- Nếu implementation sửa `.codex/agents/registry.json` hoặc `.codex/schemas/design-analysis-result.schema.json`
- Thì scope bị vượt quá proposal.
- Nếu implementation thêm dependency hoặc runtime wrapper cho `spawn_agent`
- Thì scope bị vượt quá non-goals.
- Nếu final diff không chứa scenario cho rejected spawn pattern
- Thì acceptance criteria chưa đạt.

## Expected Outcomes

- Change có đủ scenario coverage để `/apply` dùng làm implementation context.
- Verification tập trung vào OpenSpec validation và diff scope.
- Không chạy implementation trực tiếp từ `/tdd`.

## Notes for Implementation

- Ưu tiên diff nhỏ.
- Dùng `rg`/`git diff --stat`/`openspec validate` để verify nhanh.

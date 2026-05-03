# Test Scenario: design-agent-contracts-runtime

- Source tasks:
  - 2.1 Apply the `design-skill` delta spec for schema-safe managed specialist spawning.
  - 2.2 Apply the `design-agent-contracts` delta spec for explicit Codex spawn constraints and retry behavior.
- Related requirements:
  - `design-agent-contracts`: Document runtime contract for design orchestration
- Assumptions:
  - Runtime contract lives in `/design` documentation and OpenSpec specs.
  - Existing core, optional, parallel, serialized, and failure behavior remains unchanged.

## Happy Path

- Khi reviewer mở updated `design-agent-contracts` spec
- Thì requirement runtime contract vẫn giữ các scenario hiện có: core agents, optional agents, parallel behavior, failure handling.
- Thì spec thêm scenario cho Codex spawn schema constraints.
- Thì spec thêm scenario cho retry behavior khi spawn schema bị reject.

## Edge Cases

- Nếu reviewer archive change, updated requirement block không được mất scenario cũ.
- Nếu optional agent failure xảy ra sau retry schema-safe, failure policy hiện có vẫn áp dụng.
- Nếu parallel execution không khả dụng, serialized fallback vẫn preserve isolated outputs.

## Error Cases

- Nếu delta spec chỉ thêm partial requirement block dưới `MODIFIED Requirements`
- Thì archive có nguy cơ mất scenario cũ.
- Nếu spec thiếu `#### Scenario` heading đúng cấp
- Thì OpenSpec validation hoặc review phải bắt lỗi format.

## Expected Outcomes

- Delta specs mô tả đầy đủ behavior mới mà không làm mất contract cũ.
- Spec-level behavior khớp proposal và design.
- `openspec validate fix-design-agent-spawn-schema --strict` pass.

## Notes for Implementation

- Với `MODIFIED Requirements`, copy toàn bộ requirement block hiện có rồi thêm scenario mới.
- Không tạo capability mới vì đây là thay đổi behavior của capability hiện có.

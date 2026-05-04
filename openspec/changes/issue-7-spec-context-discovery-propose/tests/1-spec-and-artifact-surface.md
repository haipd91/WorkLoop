# Test Scenario: 1 spec and artifact surface

- Source tasks:
  - 1.1 Add active delta spec coverage for `/propose` Spec Context Discovery.
  - 1.2 Update proposal artifact guidance to record related specs and active changes reviewed.
  - 1.3 Update proposal template with a concise context-reviewed section.
- Related requirements:
  - Discover existing spec context before proposal generation
  - Record discovered context in planning artifacts
- Assumptions:
  - This change defines a new `propose-skill` capability because no active spec currently owns `/propose` behavior.

## Happy Path

- Khi reviewer mở delta spec của change.
- Thì spec có requirement mô tả `/propose` phải discover existing spec context trước khi tạo artifacts.
- Thì spec có scenario cho active specs search, matched specs read, active changes search, artifact context evidence, và archive search limit.
- Khi reviewer mở proposal template/guidance sau implementation.
- Thì có section hoặc guidance rõ để ghi related active specs và related active changes ngắn gọn.

## Edge Cases

- Nếu không có related active specs.
- Thì artifact guidance vẫn yêu cầu ghi rõ không tìm thấy related specs hoặc không có relevant active specs.
- Nếu chỉ có active change hiện tại match search.
- Thì guidance phải tránh xem current change như overlap conflict.

## Error Cases

- Nếu delta spec chỉ mô tả search chung chung nhưng không yêu cầu read matched specs trước capability classification.
- Thì scenario fail vì không đủ read-before-write contract.
- Nếu proposal template khuyến khích copy dài nội dung spec liên quan.
- Thì scenario fail vì trái với constraint concise/reviewable.

## Expected Outcomes

- Spec-level behavior rõ, testable, dùng SHALL/MUST.
- Artifact surface có chỗ ghi context discovery evidence.
- Không tạo dependency hoặc artifact mới ngoài scope.

## Notes for Implementation

- Giữ context evidence ngắn: file path, requirement/context liên quan, impact ngắn.

# Test Scenario: 3 verification

- Source task: 3.1 Generate test scenarios with `/tdd ai-native-human-approval-gates`.
- Source task: 3.2 Run OpenSpec validation for `ai-native-human-approval-gates`.
- Source task: 3.3 Verify scenarios cover review-artifact-only commits, draft PR readiness, human approval, GitHub approval, and check states.
- Related requirements:
  - `github-workflow`: Quality gates.
  - `github-orchestration-skills`: End-to-end GitHub handoff order.
  - `review-quality-gate`: Define parseable review artifact contract.
  - `review-quality-gate`: Block archive on stale review.
- Assumptions:
  - `/tdd` creates scenario specs only because no test framework is detected.
  - OpenSpec validation is run later by `/apply` or a human step, not by `/tdd`.

## Happy Path

- Khi `/tdd ai-native-human-approval-gates` hoàn tất
- Thì `openspec/changes/ai-native-human-approval-gates/tests/` có scenario files covering review metadata, AI-native merge gates, và verification.
- Khi OpenSpec validation chạy cho change
- Thì proposal, design, tasks, specs, và test scenario intent nhất quán về gates và terminology.
- Khi reviewer đọc test scenarios
- Thì họ thấy coverage rõ cho review-artifact-only commits, draft PR readiness, human release approval, GitHub reviewer approval, N/A checks, và failing/pending checks.

## Edge Cases

- Một task có nhiều requirements liên quan; scenario gom theo capability thay vì tạo file quá nhỏ cho từng checkbox.
- Một requirement xuất hiện trong nhiều specs; scenario ghi rõ related requirements để tránh bỏ sót cross-capability behavior.
- Test scenario không runnable; summary phải nói rõ scenario mode được dùng vì không phát hiện framework.

## Error Cases

- Không có test file nào được tạo cho task verification.
- Scenario thiếu một trong các coverage bắt buộc: review-artifact-only commits, draft PR readiness, human approval, GitHub approval, check states.
- Scenario viết theo implementation internals thay vì observable workflow behavior.
- `/tdd` tự chạy hoặc claim OpenSpec validation đã chạy, trái guardrail.

## Expected Outcomes

- Có đủ scenario files để `/apply` dùng làm behavioral context.
- Verification task có tiêu chí kiểm tra rõ, không chỉ là checklist chung chung.
- Người review có thể xác nhận nhanh rằng các gates chính đã được test-intent coverage.

## Notes for Implementation

- `/tdd` không chạy validation hoặc tests.
- Sau khi test scenarios được duyệt, bước tiếp theo phù hợp là `/apply ai-native-human-approval-gates`.

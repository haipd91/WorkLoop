# Test Scenario: 1 review-metadata-semantics

- Source task: 1.1 Update `review-quality-gate` spec to define implementation-state metadata that can exclude `review.md`.
- Source task: 1.2 Update `/review` guidance to write implementation-state metadata and residual risk.
- Source task: 1.3 Update `/archive` guidance to allow review-artifact-only commits without stale failure.
- Related requirements:
  - `review-quality-gate`: Define parseable review artifact contract.
  - `review-quality-gate`: Block archive on stale review.
- Assumptions:
  - `review.md` remains a committed OpenSpec artifact.
  - Review metadata must identify reviewed implementation state separately from the current repository HEAD.
  - Only `openspec/changes/<name>/review.md` may be excluded from stale implementation checks.

## Happy Path

- Khi `/review` tạo `openspec/changes/<name>/review.md`
- Thì artifact có metadata parseable: `verdict`, `blocking_findings`, `reviewed_ref`, `reviewed_diff`, `reviewed_implementation_diff`, `reviewed_paths_excluded`, và `reviewed_at`.
- Thì `reviewed_paths_excluded` đúng bằng `openspec/changes/<name>/review.md`.
- Thì artifact có các section: Findings, Tests Run, Residual Risk, Verdict.
- Khi `review.md` được commit sau review và không có file implementation nào đổi thêm
- Thì `/archive` và `/github:merge` không block vì `HEAD` khác `reviewed_ref`.

## Edge Cases

- `review.md` được thêm mới sau review, nhưng không có file khác đổi.
- `review.md` được chỉnh lại nội dung residual risk hoặc metadata formatting, nhưng implementation-state metadata vẫn hợp lệ và implementation files không đổi.
- `reviewed_ref` khác `HEAD`, nhưng pathset sau review chỉ chứa `openspec/changes/<name>/review.md`.
- `review.md` chứa verdict hợp lệ nhưng có residual risk cần human release approval; archive vẫn đọc được risk thay vì coi artifact malformed.

## Error Cases

- `review.md` thiếu implementation-state metadata.
- `review.md` có metadata trùng lặp hoặc mâu thuẫn, ví dụ hai verdict khác nhau.
- `review.md` dùng giá trị metadata không xác định.
- Sau review có thay đổi ở file ngoài `openspec/changes/<name>/review.md`.
- Path loại trừ quá rộng, ví dụ loại trừ cả thư mục `openspec/changes/<name>/`.

## Expected Outcomes

- Review artifact hợp lệ cho phép merge/archive tiếp tục khi chỉ có review-artifact-only commit.
- Review artifact malformed block archive với lý do rõ ràng.
- Non-review implementation changes sau review block merge/archive và yêu cầu chạy lại `/review`.
- Stale check dựa trên implementation state, không dựa máy móc vào việc `HEAD == reviewed_ref`.

## Notes for Implementation

- Không dùng `reviewed_ref` đơn lẻ làm stale gate.
- So sánh pathset/fingerprint và chỉ exclude đúng `openspec/changes/<name>/review.md`.
- Hướng dẫn `/review` phải ghi residual risk đủ rõ để `/github:merge` có thể xin human release approval trên risk thật.

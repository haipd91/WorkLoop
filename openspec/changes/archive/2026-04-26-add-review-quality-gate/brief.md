# Brief: Add Review Quality Gate

## Problem

Sau `/apply` không có bước review bắt buộc. Code có thể được archive dù chưa kiểm tra scope, correctness, tests, hoặc rủi ro còn lại. Điều này làm workflow dễ chấp nhận implementation sai intent, thiếu test, hoặc có side effect ngoài phạm vi.

## Goal

Thêm quality gate sau apply và trước archive. Mỗi change đã apply phải có review record rõ ràng về findings, tests run, residual risk, và verdict. Archive chỉ được tiếp tục khi review pass và không còn blocking findings.

## Scope

### In scope

- Thêm bước review sau apply và trước archive.
- Review kiểm tra diff so với proposal, spec, và tasks.
- Review ghi findings, tests run, residual risk, và verdict.
- Archive bị chặn nếu thiếu review hoặc còn blocking findings.

### Out of scope

- Không build hệ thống CI mới.
- Không yêu cầu reviewer người thật.
- Không refactor toàn bộ OpenSpec workflow.
- Không sửa implementation logic của app ngoài workflow/skill docs.

## Constraints

- Giữ thay đổi nhỏ, chủ yếu trong skill docs/workflow docs.
- Review là code-review stance, ưu tiên bugs, risks, regressions, và missing tests.
- Không thêm dependency.
- Phải phù hợp OpenSpec flow hiện tại.
- Archive skill phải kiểm tra review gate trước khi archive.

## Acceptance Criteria

- [ ] Workflow docs mô tả flow apply -> review -> archive.
- [ ] Có artifact review.md hoặc tương đương cho mỗi change đã apply.
- [ ] review.md ghi findings, tests run, residual risk, verdict.
- [ ] Archive từ chối nếu review.md thiếu.
- [ ] Archive từ chối nếu verdict không pass hoặc còn blocking findings.
- [ ] Apply docs/skill hướng dẫn user chạy review sau implementation.

# Test Scenario: 2 ai-native-merge-gates

- Source task: 2.1 Update `github-workflow` spec to define Codex-owned mechanical steps and human release approval.
- Source task: 2.2 Update `github-orchestration-skills` spec for draft PR readiness, approval semantics, and N/A checks.
- Source task: 2.3 Update `/github:merge` guidance to mark draft PRs ready when gates pass.
- Source task: 2.4 Update `/github:merge` guidance to require GitHub reviewer approval only when branch protection or repo policy requires it.
- Source task: 2.5 Update `/github:merge` guidance to treat missing checks as N/A when no required checks are configured or exposed.
- Source task: 2.6 Update `/apply` handoff so GitHub-backed changes route to `/github:create-pr` before `/review`.
- Related requirements:
  - `github-workflow`: Quality gates.
  - `github-orchestration-skills`: End-to-end GitHub handoff order.
- Assumptions:
  - Codex can inspect PR draft state, reviews, unresolved feedback, checks/statuses, issue link, and local review gate evidence.
  - GitHub branch protection remains authoritative when it requires reviewer approval or checks.
  - Human release approval is a material risk/release decision, not a request to click mechanical GitHub controls.

## Happy Path

- Khi `/github:merge` kiểm tra một draft PR và thấy review, comment, check, issue, và local review gates đều pass
- Thì `/github:merge` mark PR ready for review trước khi merge thay vì block để human tự xử lý draft state.
- Khi repo không yêu cầu GitHub reviewer approval qua branch protection hoặc policy rõ ràng
- Thì `/github:merge` không block chỉ vì thiếu GitHub approval review.
- Khi không có required checks hoặc statuses được GitHub expose cho PR head commit
- Thì `/github:merge` ghi trạng thái checks là N/A và tiếp tục tới human release approval.
- Khi mọi gate đã pass
- Thì `/github:merge` chỉ hỏi human release approval cho material risk hoặc release acceptance, rồi Codex tự thực hiện merge/archive handoff.
- Khi `/apply` hoàn tất một GitHub-backed change
- Thì handoff tiếp theo là `/github:create-pr <change-name>`, không phải `/review <change-name>`.

## Edge Cases

- PR đã ready sẵn; `/github:merge` không cần đổi draft state nhưng vẫn kiểm tra đầy đủ gates.
- PR có optional checks không required và không fail theo policy; `/github:merge` không coi đó là blocker bắt buộc.
- Không đọc được branch protection đầy đủ, nhưng GitHub merge API sau đó reject vì required rule; `/github:merge` phải block và báo reason từ GitHub.
- Human đã approval release ngay trong merge request hiện tại; `/github:merge` không hỏi lại cùng approval.
- GitHub approval tồn tại nhưng có unresolved requested changes/comment threads; `/github:merge` vẫn route qua `/github:address-comments`.
- Change được human accept là local-only; `/apply` có thể handoff sang `/review <change-name>`.

## Error Cases

- Required CI check fail hoặc pending.
- Branch protection hoặc repo policy yêu cầu reviewer approval nhưng PR chưa có approval hợp lệ.
- PR có unresolved actionable feedback hoặc requested changes.
- Local review gate thiếu `review.md`, review artifact malformed, hoặc review stale do non-review implementation changes.
- Human không approve release risk khi `/github:merge` hỏi.
- `/apply` không xác định được change có GitHub-backed hay local-only; phải hỏi thay vì đoán handoff.

## Expected Outcomes

- Draft state không còn là blocker thủ công khi gates đã pass.
- GitHub reviewer approval chỉ required khi branch protection hoặc repo policy required.
- Missing checks được phân loại N/A khi không có required checks/statuses exposed, không bị gọi là unreadable CI.
- Required failing/pending checks vẫn block trừ khi human explicit accept documented bypass.
- Merge/archive chỉ diễn ra sau human release approval hoặc approval đã có trong request.
- GitHub-backed workflow giữ đúng thứ tự `/apply` -> `/github:create-pr` -> `/review`.

## Notes for Implementation

- Tách ba khái niệm: GitHub reviewer approval, human release approval, và mechanical PR readiness.
- Nếu phải bypass failing/pending checks, guidance phải yêu cầu documented human acceptance rõ ràng.
- Khi Codex thực hiện mechanical step, output cần ghi gate nào pass, gate nào N/A, gate nào cần human approval.

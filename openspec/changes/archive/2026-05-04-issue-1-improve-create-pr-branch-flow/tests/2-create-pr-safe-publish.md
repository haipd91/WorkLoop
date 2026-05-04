# Test Scenario: 2 create-pr-safe-publish

- Source task: 2.3 Add safe staging, commit, push, and draft PR creation guidance.
- Source task: 2.4 Keep blockers for missing issue linkage, unclear scope, unsafe branch publication, missing verification, or missing residual risk.
- Related requirements:
  - `github-orchestration-skills`: Create PR publishes local work.
  - `github-orchestration-skills`: Create PR cannot publish safely.
- Assumptions:
  - GitHub MCP/plugin is available for PR create/update.
  - Local git commands are available for branch, stage, commit, and push.
  - The PR body shape still requires issue link, OpenSpec path, problem, approach, verification, and residual risk.

## Happy Path

- Khi `/github:create-pr` có safe work branch, issue linkage, OpenSpec change path, scoped local changes, verification notes, và residual risk
- Thì skill stage đúng files thuộc scope change.
- Thì skill tạo intentional commit với message liên quan issue/change.
- Thì skill push branch lên remote.
- Thì skill tạo hoặc update draft PR qua GitHub MCP/plugin.
- Thì PR body có đủ linked issue, OpenSpec path, problem, approach, verification, residual risk, và links.

## Edge Cases

- Đã có draft PR cho branch hiện tại; skill update PR body thay vì tạo PR trùng.
- Không có file code runtime, chỉ có skill/docs/OpenSpec artifacts; vẫn publish nếu scope và verification rõ.
- Có untracked unrelated OpenSpec change khác; skill không stage file đó và phải ghi rõ nếu bỏ qua hoặc block nếu không phân biệt được.
- Verification có lệnh pass và một residual risk rõ; PR vẫn hợp lệ dù không có runnable tests.

## Error Cases

- Missing issue linkage và human chưa accept missing linkage.
- OpenSpec change path không tồn tại.
- Verification section sẽ bị bỏ trống hoặc chỉ ghi chung chung.
- Residual risk bị bỏ trống.
- Dirty worktree chứa unrelated changes không thể tách an toàn.
- GitHub MCP/plugin không có quyền tạo/update PR.
- Branch push thất bại hoặc upstream không rõ.

## Expected Outcomes

- Local work chỉ được commit/push khi scope rõ.
- PR draft không được tạo với body thiếu verification hoặc residual risk.
- Missing issue, missing OpenSpec path, unsafe dirty state, hoặc branch publication failure block trước khi mutate remote.
- Handoff luôn nêu current state, done, needs human, next command, blockers, links.

## Notes for Implementation

- Stage bằng path cụ thể; không dùng publish-whole-worktree nếu có unrelated changes.
- Nếu phải block, không tạo commit “partial” làm worktree khó hiểu hơn.
- PR create/update phải dùng GitHub MCP/plugin, không custom HTTP client.

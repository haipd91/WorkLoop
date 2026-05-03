# GitHub Orchestration Workflow

## 1. Mục tiêu

- Tạo lớp skill GitHub chạy quanh workflow OpenSpec hiện có.
- Giữ core OpenSpec skills tập trung vào artifact trong repo.
- Đưa các thao tác issue, PR, review comments, merge, close issue vào các skill GitHub riêng.
- Sau mỗi bước, output handoff rõ để human biết trạng thái hiện tại và lệnh tiếp theo.

## 2. Mental Model

```text
GitHub issue
  |
  v
OpenSpec workflow
  |
  v
GitHub PR
  |
  v
review / comments / checks
  |
  v
fix + reply + push
  |
  v
merge + close issue
  |
  v
OpenSpec archive
```

GitHub là source of truth cho issue, PR, review, merge state. OpenSpec vẫn là source of truth cho planning, spec, tasks, tests, review artifact trong repo.

## 3. End-to-End Flow

```text
/github:create-issue
  |
  v
/brief <change-name>
  |
  v
/propose <change-name>
  |
  v
/tdd <change-name>
  |
  v
/apply <change-name>
  |
  v
/github:create-pr
  |
  v
/review <change-name>
  |
  v
/github:post-review
  |
  v
/github:sync-review
  |
  |-- feedback -> /github:address-comments
  |-- clean    -> /github:merge
  |
  v
/archive <change-name>
```

Flow này không bắt buộc cho mọi thay đổi nhỏ. Dùng khi work cần GitHub issue/PR làm tracking và review surface.

## 4. Core OpenSpec Skills vs GitHub Orchestration Skills

| Nhóm | Skills | Trách nhiệm |
| --- | --- | --- |
| Core OpenSpec | `/brief`, `/propose`, `/tdd`, `/apply`, `/review`, `/archive` | Tạo, triển khai, kiểm tra, review, archive OpenSpec artifacts |
| GitHub orchestration | `/github:create-issue`, `/github:create-pr`, `/github:post-review`, `/github:sync-review`, `/github:address-comments`, `/github:merge` | Tạo issue/PR, đồng bộ review state, xử lý comments, merge/close issue, handoff |

Không rewrite core OpenSpec skills để sở hữu GitHub state. GitHub skills chèn vào trước, giữa, và sau OpenSpec workflow.

## 5. Read-Only vs Mutating Skills

| Skill | Loại | Được làm |
| --- | --- | --- |
| `/github:create-issue` | Mutating | Tạo GitHub issue |
| `/github:create-pr` | Mutating | Tạo hoặc update draft PR |
| `/github:post-review` | Mutating | Post review summary lên PR |
| `/github:sync-review` | Read-only | Đọc PR state, comments, requested changes, checks; tạo action plan |
| `/github:address-comments` | Mutating | Sửa local files, verify, push, reply comments khi MCP hỗ trợ |
| `/github:merge` | Mutating | Kiểm tra gates, merge PR, close issue nếu cần |

Rule:

- Read-only skill không được edit file, reply comment, push, update PR, hoặc đổi GitHub state.
- Mutating skill phải nói rõ target và action trước khi mutate nếu repo/PR/issue chưa rõ.
- Tất cả GitHub operations phải dùng MCP GitHub/plugin.
- Không nhận token/secrets qua chat.

## 6. Skill Contracts

### 6.1 `/github:create-issue`

- Input: mô tả việc cần làm.
- Output:
  - issue number
  - issue URL
  - suggested OpenSpec change name
  - next command
- Block nếu thiếu repo, thiếu permission, hoặc issue input quá mơ hồ mà human không chấp nhận draft/incomplete issue.

### 6.2 `/github:create-pr`

- Input: branch hiện tại, issue link, OpenSpec change path.
- Output:
  - draft PR URL
  - PR body có problem, approach, verification, OpenSpec path, residual risk, links
  - next command
- Block nếu thiếu issue link hoặc OpenSpec path mà human chưa chấp nhận.

### 6.2.1 `/apply` handoff for GitHub-backed changes

- Khi apply tasks complete cho change có GitHub issue/PR context:
  - next command phải là `/github:create-pr <change-name>`
  - không handoff thẳng sang `/review`
- `/review` chạy sau khi PR đã tồn tại, để review artifact có thể được post/sync vào GitHub source of truth.
- Local-only exception chỉ dùng `/review` sau `/apply` khi human đã accept rõ việc bỏ qua GitHub issue/PR tracking.

### 6.3 `/github:post-review`

- Input: PR target + `openspec/changes/<change>/review.md`.
- Output:
  - review summary posted to PR
  - verdict
  - blocking findings state
  - tests run
  - residual risk
- Block nếu `review.md` thiếu hoặc malformed.

### 6.4 `/github:sync-review`

- Input: PR target.
- Output:
  - PR state
  - inline file comments
  - general PR/review comments
  - requested changes
  - CI/check status nếu MCP expose
  - action plan
- Không mutate.

### 6.5 `/github:address-comments`

- Input: PR target hoặc action plan từ `/github:sync-review`.
- Output:
  - comments addressed
  - files changed
  - verification run
  - comments replied when MCP supports it
  - branch pushed
- Stop hỏi human nếu comment mơ hồ, conflict scope, hoặc cần product decision.

### 6.6 `/github:merge`

- Input: PR target + OpenSpec change name.
- Gates:
  - PR review state OK
  - no unresolved blocking comments
  - required checks pass hoặc human accepted documented bypass
  - no required checks/statuses exposed => checks N/A, không block
  - draft PR ready gates pass => Codex mark ready, không bắt human click
  - GitHub reviewer approval chỉ bắt buộc khi branch protection/repo policy yêu cầu
  - human release approval explicit trước merge
  - `review.md` exists
  - `verdict: pass`
  - `blocking_findings: none`
  - review implementation-state metadata không stale; commit chỉ thêm/sửa `review.md` không block
  - issue linkage known hoặc accepted missing
- Output:
  - merged PR
  - issue closed nếu GitHub không auto-close
  - next command: `/archive <change-name>`

## 7. Handoff Contract

Mọi GitHub orchestration skill phải kết thúc bằng:

```md
## Handoff

- Current state:
- Done:
- Needs human:
- Next command:
- Blockers:
- Links:
```

Blocked state vẫn phải có handoff. Không được kết thúc bằng trạng thái mơ hồ.

## 8. Handoff Examples

### 8.1 Issue created

```md
## Handoff

- Current state: GitHub issue created.
- Done: Created issue #123 with problem, goal, and acceptance criteria.
- Needs human: Confirm issue scope if needed.
- Next command: `/brief issue-123-add-github-orchestration`
- Blockers: none
- Links: https://github.com/org/repo/issues/123
```

### 8.2 Review sync blocked

```md
## Handoff

- Current state: PR review sync blocked.
- Done: Resolved PR #45 but could not read review comments.
- Needs human: Enable GitHub MCP/plugin permission for PR comments.
- Next command: `/github:sync-review https://github.com/org/repo/pull/45`
- Blockers: Missing PR comments permission.
- Links: https://github.com/org/repo/pull/45
```

### 8.3 Comments addressed

```md
## Handoff

- Current state: Review comments addressed and branch pushed.
- Done: Fixed 2 inline comments, replied to 1 general comment, ran relevant verification.
- Needs human: Re-check PR review state.
- Next command: `/github:sync-review https://github.com/org/repo/pull/45`
- Blockers: none
- Links: https://github.com/org/repo/pull/45
```

### 8.4 PR merged

```md
## Handoff

- Current state: PR merged and issue closed.
- Done: Merged PR #45 and closed issue #123.
- Needs human: Run archive when ready.
- Next command: `/archive issue-123-add-github-orchestration`
- Blockers: none
- Links: https://github.com/org/repo/pull/45, https://github.com/org/repo/issues/123
```

## 9. Boundaries

Out of scope for the first orchestration layer:

- GitHub Actions enforcement.
- Issue/PR templates.
- GitHub Projects bidirectional sync.
- Custom GitHub HTTP clients.
- Auto-fix ambiguous comments.
- Rewriting core OpenSpec skills.

Các phần này nên là follow-up issue/change riêng nếu cần.

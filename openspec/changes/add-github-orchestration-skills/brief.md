# Brief: Add GitHub Orchestration Skills

## Problem

WorkLoop hien co OpenSpec workflow tot cho local planning/apply/review/archive va da co GitHub source-of-truth contract, nhung chua co lop thao tac GitHub thuc te. Nguoi dung can tao issue, tao PR, post review, lay review comments, fix, reply comments, merge PR, dong issue, va handoff ro de human biet buoc tiep theo.

Neu nhet toan bo GitHub behavior vao cac core skill hien tai (`/brief`, `/propose`, `/apply`, `/review`, `/archive`) thi scope de phinh to va lam workflow kho kiem soat. Can tao cac GitHub orchestration skills rieng de chen vao truoc, giua, va sau OpenSpec workflow, dong thoi cap nhat docs workflow de human biet dung chung o dau.

## Goal

Tao bo GitHub orchestration skills dung MCP GitHub/plugin de van hanh workflow thuc te:

- Tao GitHub issue tu thong tin can lam.
- Tao draft PR tu work branch.
- Post review len PR.
- Sync review comments/CI/review state tu PR.
- Address comments, fix bug, reply comments, push lai branch.
- Merge PR va dong issue.
- Output handoff ro sau moi buoc de human biet can lam gi tiep theo.
- Cap nhat docs workflow he thong de mo ta cach cac GitHub skills chen vao OpenSpec workflow.

## Scope

### In scope

- Tao cac skill GitHub orchestration moi:
  - `/github:create-issue`
  - `/github:create-pr`
  - `/github:post-review`
  - `/github:sync-review`
  - `/github:address-comments`
  - `/github:merge`
- Moi thao tac GitHub phai dung MCP GitHub/plugin.
- Skill khong duoc yeu cau user paste token truc tiep vao chat.
- `/github:create-issue` tao issue tu mo ta cong viec va tra ve issue URL/number + suggested OpenSpec change name.
- `/github:create-pr` tao hoac cap nhat draft PR voi issue link, OpenSpec path, summary, verification, residual risk.
- `/github:post-review` doc `review.md` hoac review output roi post summary/findings/verdict len GitHub PR.
- `/github:sync-review` doc PR review state, inline comments, general comments, requested changes, CI/check status, roi tao action plan read-only.
- `/github:address-comments` xu ly inline/general comments, sua local files khi ro, chay verification phu hop, reply comments, push lai branch.
- `/github:merge` kiem tra gates roi merge PR, dong issue neu can, va handoff sang `/archive`.
- Support ca inline file comments va general PR/review comments.
- Moi skill GitHub phai co handoff output contract:
  - current state
  - done
  - needs human
  - next command
  - blockers
  - links
- Error/blocked state van phai output handoff ro.
- Update docs workflow he thong de mo ta GitHub orchestration flow:
  - create issue
  - OpenSpec workflow
  - create PR
  - post/sync review
  - address comments/fix CI neu co
  - merge/close issue
  - archive
- Docs phai phan biet:
  - core OpenSpec skills
  - GitHub orchestration skills
  - read-only vs mutating GitHub skills
  - handoff contract
- Link hoac update docs hien co de consistent voi GitHub source-of-truth workflow.

### Out of scope

- Rewrite core OpenSpec skills (`/brief`, `/propose`, `/apply`, `/review`, `/archive`).
- Them `/github:link-work`.
- Custom GitHub HTTP client ngoai MCP GitHub/plugin.
- GitHub Projects bidirectional sync phuc tap.
- Tu dong fix comment mo ho ma khong hoi human.
- GitHub Actions enforcement.
- Issue/PR templates neu khong can de hoan thanh orchestration skills dau tien.

## Constraints

- GitHub repo la source of truth cho issue/PR/review/merge state.
- OpenSpec artifacts van la structured repo artifacts.
- GitHub operations phai di qua MCP GitHub/plugin.
- Token/secrets khong duoc paste vao chat; phai dung connector/env/config an toan.
- Skill read-only khong duoc mutate repo/GitHub:
  - `/github:sync-review` la read-only.
- Skill mutating phai noi ro khi sua/push/reply/merge:
  - `/github:create-issue`
  - `/github:create-pr`
  - `/github:post-review`
  - `/github:address-comments`
  - `/github:merge`
- Neu MCP/GitHub unavailable hoac thieu permission, skill phai fail ro va handoff blocker.
- Neu review comment mo ho hoac scope conflict, skill phai hoi human thay vi doan.
- Diffs phai nho, tung skill review duoc.

## Acceptance Criteria

- [ ] Co skill docs/instructions cho `/github:create-issue`.
- [ ] Co skill docs/instructions cho `/github:create-pr`.
- [ ] Co skill docs/instructions cho `/github:post-review`.
- [ ] Co skill docs/instructions cho `/github:sync-review`.
- [ ] Co skill docs/instructions cho `/github:address-comments`.
- [ ] Co skill docs/instructions cho `/github:merge`.
- [ ] Moi skill quy dinh dung MCP GitHub/plugin cho thao tac GitHub.
- [ ] Moi skill quy dinh khong nhan token/secrets qua chat.
- [ ] Moi skill co handoff output contract gom current state, done, needs human, next command, blockers, links.
- [ ] Blocked/error states van co handoff.
- [ ] `/github:sync-review` duoc dinh nghia la read-only.
- [ ] `/github:address-comments` support inline file comments va general PR/review comments.
- [ ] `/github:address-comments` dung hoi human khi comment mo ho hoac scope conflict.
- [ ] `/github:merge` kiem tra gates truoc merge va dong issue neu can.
- [ ] Flow end-to-end duoc document: create issue -> OpenSpec workflow -> create PR -> review/post/sync -> address comments -> merge -> archive.
- [ ] System workflow docs show where each GitHub orchestration skill fits around OpenSpec workflow.
- [ ] Docs distinguish core OpenSpec skills from GitHub orchestration skills.
- [ ] Docs explain read-only vs mutating GitHub skills.
- [ ] Docs include handoff contract and example handoffs.
- [ ] Existing GitHub source-of-truth doc links to the orchestration workflow or is updated consistently.

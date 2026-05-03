# Brief: GitHub Source Of Truth Workflow

## Problem

WorkLoop hien co OpenSpec workflow nhung chua dinh nghia GitHub issue/PR la source of truth. Vi vay Codex co the van hanh roi rac: thong tin nam trong chat, issue, PR, hoac OpenSpec artifacts nhung khong co lifecycle ro.

## Goal

Co docs/spec ro cho lifecycle GitHub-first: issue -> OpenSpec change -> branch/PR -> review -> archive. Noi dung du cu the de cac skill sau nay dua vao khi tich hop GitHub workflow.

## Scope

### In scope

- Dinh nghia GitHub issue/PR la source of truth cho workflow.
- Mo ta lifecycle issue -> OpenSpec change -> PR -> review -> archive.
- Dinh nghia vai tro cua issue, branch, PR, commit, OpenSpec artifacts.
- Dinh nghia naming conventions cho issue/change/branch/PR.
- Dinh nghia artifact ownership: thong tin nao nam o GitHub, thong tin nao nam o OpenSpec files.
- Dinh nghia quality gates truoc PR/merge/archive.
- Ghi ro automation la future scope.

### Out of scope

- Implement GitHub automation.
- Sua behavior cua cac skill hien co.
- Tao hoac sua GitHub Actions.
- Migrate issue templates that.
- Tu dong sync task/status giua OpenSpec va GitHub.

## Constraints

- Chi docs/spec, khong sua application behavior.
- Phai dung OpenSpec artifacts hien co.
- GitHub repo la source of truth.
- Durable decisions phai nam trong repo hoac GitHub, khong chi nam trong chat.
- Diffs phai nho, review duoc.

## Acceptance Criteria

- [ ] Co OpenSpec spec/docs mo ta GitHub source-of-truth workflow.
- [ ] Lifecycle issue -> OpenSpec change -> branch/PR -> review -> archive duoc dinh nghia ro.
- [ ] Vai tro cua issue, PR, branch, commit, OpenSpec artifacts duoc mo ta.
- [ ] Naming conventions cho issue/change/branch/PR duoc ghi ro.
- [ ] Artifact ownership duoc dinh nghia: GitHub giu gi, OpenSpec giu gi.
- [ ] Quality gates truoc PR/merge/archive duoc ghi ro.
- [ ] Out-of-scope automation/skill behavior/GitHub Actions/template migration duoc ghi ro.

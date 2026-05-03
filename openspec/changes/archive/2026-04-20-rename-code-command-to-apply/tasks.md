## 1. Command surface

- [x] 1.1 Locate the active user-facing command registration for the implementation step and replace `/code` with `/apply`
- [x] 1.2 Remove `/code` from the supported command surface instead of forwarding it to `/apply`
- [x] 1.3 Preserve the existing implementation artifact-loading behavior under `/apply`, including optional `tests/` context

## 2. Workflow references

- [x] 2.1 Update `.codex/skills/` instructions and prompts that reference `/code` so they consistently point to `/apply`
- [x] 2.2 Update `.claude/skills/` instructions and prompts that reference `/code` so the mirrored skill set stays aligned
- [x] 2.3 Update workflow docs and help text, including `docs/team-ai-workflow.md`, to describe `/apply` as the only implementation command

## 3. Spec and regression checks

- [x] 3.1 Update implementation-related specs and any generated workflow artifacts required by the new `/apply` contract
- [x] 3.2 Search the repository for stale user-facing `/code` references and remove or replace them when they are part of the active workflow
- [x] 3.3 Validate the end-to-end workflow handoff now reads as `/propose` -> `/design` or `/tdd` -> `/apply` without implying `/code` support

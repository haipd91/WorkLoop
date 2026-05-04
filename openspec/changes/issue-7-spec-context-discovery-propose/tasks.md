## 1. Spec and Artifact Surface

- [x] 1.1 Add active delta spec coverage for `/propose` Spec Context Discovery.
- [x] 1.2 Update proposal artifact guidance to record related specs and active changes reviewed.
- [x] 1.3 Update proposal template with a concise context-reviewed section.

## 2. Propose Workflow Guidance

- [x] 2.1 Update `.codex/skills/propose/SKILL.md` to run Spec Context Discovery before artifact generation.
- [x] 2.2 Define request/brief term extraction for domain, action, entity, UI surface, and likely capability terms.
- [x] 2.3 Define active spec search and matched-spec read behavior before capability classification.
- [x] 2.4 Define active change overlap search while excluding archived changes by default.
- [x] 2.5 Define clarification behavior when capability mapping or active-change overlap is ambiguous.
- [x] 2.6 Define archive search as optional and rationale-driven only.

## 3. Schema Instruction Alignment

- [x] 3.1 Update `openspec/schemas/spec-driven-custom/schema.yaml` proposal instructions to require discovery before capability selection.
- [x] 3.2 Update specs instructions to require using discovered context for `ADDED`, `MODIFIED`, `REMOVED`, and `RENAMED` choices.
- [x] 3.3 Keep schema and skill wording aligned so `openspec instructions proposal/specs` does not weaken `/propose` guidance.

## 4. Verification

- [x] 4.1 Run OpenSpec validation for `issue-7-spec-context-discovery-propose`.
- [x] 4.2 Search active workflow instructions for `/propose` paths that create artifacts without discovery.
- [x] 4.3 Verify archive search is not required by default in active guidance.
- [x] 4.4 Verify generated artifact guidance remains concise and does not introduce embedding/vector dependencies.

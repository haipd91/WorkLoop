## Context

`/design` already documents a manager-controlled specialist-agent workflow and a runtime contract for core, optional, parallel, serialized, and failure behavior. The missing piece is Codex-specific spawn safety: the skill does not warn that `fork_context: true` can conflict with an explicit `agent_type`, which can reject the first specialist spawn before analysis starts.

This change is documentation/spec only. It updates the `/design` skill guidance and OpenSpec delta specs so future `/design` runs use a schema-safe managed sub-agent pattern.

## Goals / Non-Goals

**Goals:**

- Make the safe Codex spawn pattern explicit in `/design`.
- Preserve the existing orchestrator-owned design workflow.
- Keep specialist outputs isolated while requiring each specialist to read workspace context from provided paths.
- Document retry behavior for spawn schema rejection.

**Non-Goals:**

- Do not change Codex runtime tool schema.
- Do not implement automatic spawn validation.
- Do not change agent registry metadata or `DesignAnalysisResult`.
- Do not redesign `/design` beyond the spawn guidance.

## Decisions

1. Add guidance in `.codex/skills/design/SKILL.md` under `Run specialist analysis`.

   Rationale: the failure occurs during runtime dispatch, so the rule belongs next to the dispatch instructions, not only in generic guardrails.

   Alternative considered: add only a guardrail at the bottom of the skill. Rejected because it is easier to miss while following the step-by-step execution section.

2. Require path-based context passing for specialist agents.

   The orchestrator should pass:

   - workspace path
   - change name
   - relevant artifact paths
   - specialist definition path
   - output schema path

   Rationale: specialists can read exactly the context they need without relying on forked conversation context.

   Alternative considered: rely on `fork_context` for context sharing. Rejected because it is the source of the observed schema rejection when combined with explicit agent type.

3. Document retry behavior as "retry without forked context".

   Rationale: this matches the known successful recovery path and keeps the workflow moving without changing runtime tooling.

   Alternative considered: stop and ask the user after spawn rejection. Rejected because the retry path is deterministic and low risk.

## Risks / Trade-offs

- Runtime behavior is still prompt-enforced, not mechanically validated -> Mitigation: capture requirements in specs and add `/tdd` scenarios before implementation.
- Specialists may miss context if the orchestrator passes incomplete paths -> Mitigation: list required prompt inputs explicitly.
- This does not prevent other future spawn schema mistakes -> Mitigation: keep the rule scoped to the observed conflict and avoid overgeneralizing undocumented tool behavior.

## Migration Plan

- Update the `/design` skill documentation.
- Update relevant OpenSpec specs through this change.
- No runtime migration or rollback is required.

Rollback: revert the docs/spec changes if the Codex spawn schema behavior changes.

## Open Questions

- None. The accepted runtime pattern is: no forked context when using explicit specialist agent type; pass paths and let each specialist read workspace context.

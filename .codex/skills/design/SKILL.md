---
name: design
description: Analyze a change and produce or refine a reviewable design.md before implementation. Use when the user wants a technical design handoff between /propose and /apply.
license: MIT
compatibility: Claude Code with AskUserQuestion tool support.
metadata:
  author: agentica
  version: "1.0"
---

Create or refine `design.md` for an OpenSpec change from its existing artifacts.

`/design` is a design-review step, not an implementation step. Its job is to turn
change intent into a concrete technical handoff that human can review before
`/tdd` or `/apply`.

`/design` uses a manager-controlled multi-agent pattern:

- one **Design Orchestrator** owns the run
- specialist agents contribute scoped analysis
- only the orchestrator synthesizes and writes `design.md`

Specialist agent definitions live in `*/agents/`:

- `*/agents/architecture.md`
- `*/agents/impact.md`
- `*/agents/data-flow.md`
- `*/agents/failure-modes.md`
- `*/agents/review-docs.md`
- `*/agents/security.md`
- `*/agents/performance.md`

Contract artifacts:

- `*/agents/registry.json` is the registry for available `/design` specialist agents, including role, trigger, inputs, output type, parallel group, and failure policy.
- `*/schemas/design-analysis-result.schema.json` is the output contract for every specialist agent result.
- `*/fixtures/design/` contains contract fixtures showing how input artifacts map to expected `design.md` sections.

Default collaboration pattern:

- **agent-as-tool / managed sub-agent** is the default
- **handoff** is reserved for branches where a specialist must own the next response

---

## Steps

### 1. Select the change

If a name is provided, use it. Otherwise:

- Infer from conversation context
- If ambiguous, run `openspec list --json` and use **AskUserQuestion tool** to let user select

Always announce: `Using change: <name>`.

### 2. Read change artifacts

Read these files if they exist:

- `openspec/changes/<name>/proposal.md`
- `openspec/changes/<name>/tasks.md`
- `openspec/changes/<name>/brief.md`
- `openspec/changes/<name>/design.md`
- `openspec/changes/<name>/specs/**/*.md`

Rules:

- Treat the change directory as the primary source of truth
- `proposal.md` and `tasks.md` are the minimum required inputs
- Use `brief.md` as additional context when present
- If `design.md` already exists, refine it instead of discarding it blindly

If `proposal.md` or `tasks.md` is missing, stop and explain exactly which file is missing.

### 3. Select specialist agents

The Design Orchestrator chooses which specialist agents to run.

Load `*/agents/registry.json` when present and use it as the source of truth for available specialist agents, trigger metadata, output type, and failure policy. If the registry is unavailable, fall back to the agent definition files listed below and record that fallback under `Review Notes`.

**Core agents** for most changes:

- `Architecture` -> `*/agents/architecture.md`
- `Impact` -> `*/agents/impact.md`
- `Failure Modes` -> `*/agents/failure-modes.md`

**Optional agents** when context requires them:

- `Data Flow` -> `*/agents/data-flow.md`
- `Review and Docs` -> `*/agents/review-docs.md`
- `Security` -> `*/agents/security.md`
- `Performance` -> `*/agents/performance.md`

Selection rules:

- small changes: prefer core agents only
- medium or cross-cutting changes: add `Data Flow` and `Review and Docs`
- risky changes touching auth, permissions, sensitive data, scaling, or shared infrastructure: add `Security` and/or `Performance`
- do not run more agents than the change complexity justifies
- selected agents are independent analysis workers and should be eligible for parallel execution unless a change-specific dependency is documented

### 4. Define ownership and contracts

Load the relevant agent definition files from `*/agents/` before dispatching work.

Each specialist agent must own one primary question set as defined by its file.

Use `*/schemas/design-analysis-result.schema.json` as the structured output contract when present. The schema documents the required shape; this skill does not implement runtime validation itself.

Each agent definition should be treated as an agent-surface artifact aligned with OpenAI docs:

- `name`
- `handoff_description`
- `model`
- `tools`
- `handoffs`
- `output_type`
- `instructions`

Each agent MUST return the same schema:

- `scope_analyzed`
- `findings`
- `impacted_areas`
- `assumptions`
- `risks`
- `unresolved_questions`
- `recommended_decisions`

Rules:

- Each agent stays inside its assigned responsibility
- No overlap unless the orchestrator explicitly asks for a cross-cutting note
- Free-form essays are not acceptable agent output

### 5. Run specialist analysis

The orchestrator dispatches one scoped request per selected specialist agent.

Run model:

- use managed sub-agents or agent-as-tool style collaboration by default
- run independent specialist analyses in parallel when the environment supports it
- if true parallel execution is unavailable, preserve the same separation with serialized specialist passes
- always pass workspace path, change name, relevant artifact paths, the selected agent definition file for that role, and the output schema path
- specialist agents should read their own workspace context from the paths provided in the prompt instead of relying on forked conversation context
- keep each specialist output isolated by agent name until synthesis

Codex spawn safety:

- do not combine `fork_context: true` with an explicit `agent_type` when spawning specialist agents
- if a specialist spawn is rejected because the request shape is not accepted, retry without forked context and keep the repaired specialist output isolated by agent name
- this is runtime guidance only; it does not add automatic spawn validation or change the `spawn_agent` tool schema

Use handoff only when:

- a specialist must own the next response, not just provide analysis
- that branch genuinely needs different instructions, tools, or policy

This is an exception, not the default pattern for `/design`.

Runtime contract:

- Core agents are `Architecture`, `Impact`, and `Failure Modes`.
- Optional agents are selected by trigger:
  - `Data Flow`: state, data movement, contract boundaries, handoff points, or artifact dependency concerns.
  - `Review and Docs`: workflow docs, rollout notes, approval gates, or human review dependencies.
  - `Security`: auth, authz, sensitive data, validation, abuse paths, or trust boundaries.
  - `Performance`: latency, throughput, scaling, expensive execution paths, or concurrency pressure.
- Core agent failure blocks design approval unless the human explicitly accepts the missing analysis as an unresolved question.
- Optional agent failure does not block by default, but the orchestrator must record the failed perspective under `Risks / Trade-offs` or `Open Questions`.
- If a selected agent returns output that does not match `DesignAnalysisResult`, ask that agent to repair the output once. If the repaired output is still invalid or unusable, apply that agent's failure policy.

### 6. Synthesize `design.md`

Only the Design Orchestrator may merge specialist outputs into one design draft at
`openspec/changes/<name>/design.md`.

Use this structure:

```md
## Context

## Goals / Non-Goals

**Goals:**

**Non-Goals:**

## Proposed Architecture

## Impact Map

## Decisions

## Risks / Trade-offs

## Open Questions

## Review Notes
```

Content rules:

- explain the implementation approach, not line-by-line code
- include affected modules, artifacts, or workflows in `Impact Map`
- record assumptions explicitly when context is incomplete
- include trade-offs and unresolved questions instead of hiding uncertainty
- prefer concrete decisions over generic advice
- surface conflicts instead of hiding them

Conflict resolution rules:

- use `proposal.md`, `brief.md`, and `specs/` as source-of-truth inputs
- if specialist outputs disagree and the orchestrator cannot resolve the disagreement cleanly, record it under `Open Questions`
- do not silently drop a conflicting recommendation

### 7. Human review loop

Show a short review summary before asking for approval. The summary should include:

- main design decisions
- top risks
- open questions
- changed sections if this was a refinement pass

Use **AskUserQuestion tool**:

> "Design draft is ready for review. Would you like to approve it or request changes?"

Options: `["Approve — save and proceed", "Request changes"]`

If the human requests changes:

- ask what to change
- update only the relevant sections
- show the refreshed summary
- ask again

Do not treat the design as complete until human explicitly approves it.

### 8. Confirm handoff

After approval:

1. Confirm save path: `openspec/changes/<name>/design.md`
2. Suggest next step:
   - `Run /tdd <name> to create test scenarios or test files before implementation`
   - `Run /apply <name> only after the generated tests are approved, or when the human explicitly accepts skipping /tdd for this change`

Do not auto-trigger `/tdd` or `/apply`.

---

## Guardrails

- Do not use repo-wide guessing as a substitute for change artifacts
- Do not silently overwrite important decisions from an existing `design.md`
- Do not regenerate the whole file if feedback only affects one section
- Do not present speculative decisions as certain; label assumptions clearly
- Do not let specialist agents write the final artifact directly
- Do not use handoff by default when a managed sub-agent call is enough
- Do not run optional agents without a change-specific reason
- Do not auto-approve or auto-handoff
- Keep the document optimized for human review, not for verbosity

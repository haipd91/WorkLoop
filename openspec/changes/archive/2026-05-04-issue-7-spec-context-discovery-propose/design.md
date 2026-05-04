## Context

`/propose` currently reads the current change artifacts and schema instructions, but the skill instructions do not explicitly require discovering related active specs or overlapping active changes before generating planning artifacts. The schema hints that capability selection should research existing specs, but that guidance is embedded inside artifact instructions instead of being a first-class workflow step.

This change introduces Spec Context Discovery as an explicit pre-artifact step in `/propose`. The discovery result informs capability classification, delta operation choice, and concise context evidence in generated artifacts.

## Goals / Non-Goals

**Goals:**

- Make `/propose` search active specs before choosing new vs modified capabilities.
- Make `/propose` search active changes while excluding archived changes by default.
- Require matched specs and overlapping active change artifacts to be read before planning artifacts are generated.
- Record concise discovery evidence in generated artifacts.
- Keep the workflow lightweight, deterministic, and easy to review.

**Non-Goals:**

- Add embeddings, vector search, or a semantic search service.
- Search all archived changes by default.
- Change `/apply`, `/review`, `/archive`, or GitHub orchestration behavior.
- Add a new standalone discovery artifact unless a later change proves it is needed.

## Proposed Architecture

`/propose` becomes a three-stage planning workflow:

1. Intake:
   - Resolve or create the change.
   - Read `brief.md` when present.
   - Extract search terms from request and brief: domain nouns, user actions, entities, UI/API surfaces, error/validation/security terms, and likely capability names.

2. Spec Context Discovery:
   - Search `openspec/specs/**` using the extracted terms.
   - Read matched active spec files before capability classification.
   - Search `openspec/changes/**` for related active changes while excluding `openspec/changes/archive/**`.
   - Ask for clarification when another active change overlaps or capability mapping is ambiguous.
   - Search archive only when active context is insufficient, conflicting, or missing rationale.

3. Artifact generation:
   - Generate `proposal.md`, `specs/**/*.md`, `design.md`, and `tasks.md` in the existing dependency order.
   - Use discovered context to decide new vs modified capabilities and `ADDED` vs `MODIFIED` vs `REMOVED` vs `RENAMED`.
   - Record concise related specs and related active changes in the proposal artifact.

No runtime dependency is introduced. Implementation should update instruction surfaces only unless a small helper is later justified during `/apply`.

## Impact Map

- `.codex/skills/propose/SKILL.md`
  - Add the explicit discovery stage before artifact build order.
  - Define term extraction, active spec search, active change search, clarification behavior, and archive-search limits.

- `openspec/schemas/spec-driven-custom/schema.yaml`
  - Align proposal instructions with discovery-before-capability-selection.
  - Align specs instructions with discovered-context-based delta operation choice.

- `openspec/schemas/spec-driven-custom/templates/proposal.md`
  - Add a concise `Existing Context Reviewed` section.

- `openspec/changes/issue-7-spec-context-discovery-propose/specs/propose-skill/spec.md`
  - Defines the new `propose-skill` capability until archive sync promotes it to `openspec/specs/propose-skill/spec.md`.

- Downstream workflow
  - `/tdd`, `/apply`, `/review`, and `/archive` continue to consume artifacts normally. Their behavior should not change.

## Decisions

1. Put discovery before proposal creation.

   Rationale: Capability classification happens in `proposal.md`; discovering related specs after proposal generation is too late.

   Alternative rejected: rely on `/design` to catch missed context. That would allow wrong proposal capabilities and delta specs to exist before review.

2. Use lightweight keyword/context discovery, not embeddings.

   Rationale: Current repo size and workflow needs are served by deterministic `rg`-style discovery. New semantic infrastructure would add cost, dependencies, and verification complexity without clear need.

   Alternative rejected: vector DB or embedding search. It is overbuilt for this change.

3. Search active specs first, active changes second, archive only when needed.

   Rationale: Active specs are current truth. Active changes identify in-flight conflict. Archive is historical rationale and can create noise if searched by default.

   Alternative rejected: default archive search. It risks old decisions competing with current specs.

4. Store discovery evidence in `proposal.md`.

   Rationale: Reviewers need to see the context that shaped capability choices. Proposal is the right artifact because it owns capability selection.

   Alternative rejected: duplicate the same evidence in `design.md`. Design may reference discovery when useful, but proposal should be the default durable record.

5. Leave exact search command flexible, but require paths and exclusions.

   Rationale: Skill guidance should require behavior, not overfit to one shell command. Implementation may use `rg`, CLI helpers, or another local search method as long as it searches active specs, searches active changes, and excludes archive by default.

   Alternative rejected: hardcode a single `rg` recipe as the only valid implementation. That is brittle across future tooling.

## Risks / Trade-offs

- [Risk] Keyword search misses synonyms or domain-specific names -> Mitigation: require multiple term classes and ask for clarification when mapping remains ambiguous.
- [Risk] Discovery evidence bloats proposals -> Mitigation: keep evidence to related specs, related active changes, and short impact notes.
- [Risk] Archive rationale is skipped when it matters -> Mitigation: allow archive search when active context is insufficient, conflicting, or missing rationale, and record why archive context influenced the artifacts.
- [Risk] Skill and schema instructions drift -> Mitigation: update both surfaces and verify `openspec instructions proposal/specs` still reinforces discovery.
- [Risk] Active-change search flags the current change as overlap -> Mitigation: exclude or separately identify the current change when summarizing overlaps.

## Open Questions

- None blocking. Accepted defaults:
  - Discovery evidence lives in `proposal.md` by default.
  - Exact search command remains flexible; required behavior is defined by searched paths, archive exclusion, and read-before-write contract.

## Review Notes

- Specialist perspectives applied internally: Architecture, Impact, Failure Modes, Data Flow, and Review and Docs.
- Security and Performance were not selected because the change does not alter auth, authorization, sensitive data handling, latency, throughput, or runtime scaling paths.
- Main refinement from the initial design: resolved both open questions with concrete defaults and added `Proposed Architecture` plus `Impact Map`.

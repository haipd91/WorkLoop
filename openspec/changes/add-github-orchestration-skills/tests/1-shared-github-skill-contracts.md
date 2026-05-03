# Test Scenario: Shared GitHub Skill Contracts

- Source tasks:
  - 1.1 Define the shared MCP GitHub/plugin usage rule and no-chat-secrets rule for all GitHub orchestration skills.
  - 1.2 Define the shared handoff output contract with current state, done, needs human, next command, blockers, and links.
  - 1.3 Define read-only versus mutating skill boundaries.
- Related requirements:
  - GitHub orchestration skills
  - MCP GitHub usage
  - Handoff contract
- Assumptions:
  - GitHub operations are performed through the GitHub MCP/plugin surface.
  - Skill instructions can define behavior even when the connector is unavailable at runtime.

## Happy Path

- When a GitHub orchestration skill instruction is reviewed
- Then it requires MCP GitHub/plugin for GitHub reads or writes.
- And it rejects chat-pasted tokens/secrets.
- And it includes a handoff with Current state, Done, Needs human, Next command, Blockers, and Links.
- And it states whether the skill is read-only or mutating.

## Edge Cases

- When MCP GitHub/plugin is unavailable or missing permissions
- Then the skill produces a blocked handoff with the missing connector or permission as the blocker.

- When a user attempts to provide a personal token in chat
- Then the skill rejects chat-based secret handling and directs the user to connector, environment, or configuration setup.

## Error Cases

- If any GitHub skill allows raw token handling in chat
- Then the shared security contract fails.

- If any GitHub skill performs GitHub operations through a custom HTTP client
- Then the MCP GitHub usage requirement fails.

- If a skill can complete or block without a handoff
- Then the handoff contract fails.

## Expected Outcomes

- All GitHub orchestration skills share consistent security, connector, and handoff behavior.
- Read-only and mutating boundaries are explicit before implementation.

## Notes for Implementation

- Prefer a common shared section copied into each skill over hidden implicit behavior.
- Do not rely on core OpenSpec skills to define GitHub connector behavior.

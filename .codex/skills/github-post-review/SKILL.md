---
name: github:post-review
description: Post an OpenSpec review result to a GitHub PR with verdict, findings, tests run, residual risk, and handoff.
license: MIT
compatibility: Requires GitHub MCP/plugin access and OpenSpec review artifact.
---

# GitHub Post Review

Use this skill after `/review <change-name>` has produced `review.md` and the result needs to be posted to the GitHub PR.

## Shared Contract

- Use the GitHub MCP/plugin for GitHub PR comments or review posts.
- Do not ask the user to paste tokens, personal access tokens, or secrets into chat.
- If GitHub MCP/plugin is unavailable or lacks permission, stop and produce a blocked handoff.
- Do not create a custom GitHub HTTP client.
- This is a mutating GitHub skill. State the PR target before posting.

## Input

Accept any of:

- OpenSpec change name
- PR number or URL
- repository identifier
- path to `review.md`

If PR or review artifact is unclear, ask for the missing target.

## Workflow

1. Resolve PR target.
2. Locate review artifact:
   - `openspec/changes/<change-name>/review.md`
3. Parse required review metadata:
   - `verdict`
   - `blocking_findings`
   - `reviewed_ref`
   - `reviewed_diff`
   - `reviewed_at`
4. Read required sections:
   - Findings
   - Tests Run
   - Residual Risk
   - Verdict
5. If review metadata is missing or malformed, block and hand off `/review <change-name>`.
6. Post a PR comment or review summary through GitHub MCP/plugin.
7. Return the handoff.

## PR Post Shape

```md
## OpenSpec Review

- Verdict:
- Blocking findings:
- Reviewed ref:
- Reviewed diff:
- Reviewed at:

## Findings

## Tests Run

## Residual Risk

## Links
```

## Handoff

Every result, including blocked states, must end with:

```md
## Handoff

- Current state:
- Done:
- Needs human:
- Next command:
- Blockers:
- Links:
```

Typical next command:

```text
/github:sync-review <pr>
```

## Blocked States

Block instead of posting when:

- PR target is unclear
- `review.md` does not exist
- `review.md` metadata is malformed
- review verdict is fail and the user expected a pass post
- GitHub MCP/plugin is unavailable or cannot write PR comments

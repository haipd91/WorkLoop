---
name: github:address-comments
description: Address actionable GitHub PR inline and general review comments, make scoped local fixes, verify, push, reply when supported, and hand off remaining work.
license: MIT
compatibility: Requires GitHub MCP/plugin access and local git context.
---

# GitHub Address Comments

Use this skill when GitHub PR review feedback has been synced and the user wants to fix actionable comments.

## Shared Contract

- Use the GitHub MCP/plugin for GitHub PR/comment reads and replies.
- Do not ask the user to paste tokens, personal access tokens, or secrets into chat.
- If GitHub MCP/plugin is unavailable or lacks permission, stop and produce a blocked handoff.
- Do not create a custom GitHub HTTP client.
- This is a mutating skill. It may edit local files, run verification, push the branch, and reply to comments when supported.

## Input

Accept any of:

- PR number or URL
- synced review action plan
- selected comment IDs or thread URLs
- instruction to address all clear actionable feedback

If the user does not specify which comments to address, default to all unresolved actionable comments that are clear and in scope. List ambiguous or out-of-scope comments separately.

## Workflow

1. Resolve PR target and local branch.
2. Read current review feedback through GitHub MCP/plugin:
   - inline file comments
   - general PR comments
   - general review comments
   - requested changes
3. Cluster actionable comments:
   - by file
   - by behavior
   - by docs/test concern
4. Identify stop conditions:
   - ambiguous request
   - product decision needed
   - conflict with approved OpenSpec scope
   - requested change would create unrelated refactor
   - stale inline location cannot be mapped safely
5. If stop conditions exist, ask the human before editing those items.
6. Apply focused local fixes for clear comments.
7. Run relevant verification.
8. Push the branch when fixes are ready and the user requested the GitHub workflow to proceed.
9. Reply to inline or general comments when MCP GitHub/plugin supports it.
10. Return the handoff.

## Comment Handling

- Inline file comments should map to the file and line when available.
- General PR/review comments should map to a task, artifact, or behavior area.
- Explanation-only comments should receive a drafted or posted response instead of forced code changes.
- Unsupported reply actions must appear in the handoff.

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

or after clean feedback:

```text
/review <change-name>
```

## Blocked States

Block instead of guessing when:

- PR or local branch is unclear
- feedback conflicts with OpenSpec scope
- comment is ambiguous
- human product decision is required
- GitHub MCP/plugin cannot read or reply to required comment data
- verification fails
- branch cannot be pushed safely

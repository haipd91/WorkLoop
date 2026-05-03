---
name: brief
description: AI-driven interview to create a structured brief.md through guided questioning. Use when the user wants to start a new change and needs to capture problem, goal, scope, constraints, and acceptance criteria in a standardized format.
license: MIT
compatibility: Claude Code with AskUserQuestion tool support.
metadata:
  author: agentica
  version: "1.0"
---

Conduct a guided interview to create a structured `brief.md` for the user's change.

You are an interviewer. Ask questions in natural conversational rounds, collect the user's answers, then produce a structured brief for human approval before saving.

---

## Interview Flow

### Round 1 — Problem & Goal

Ask the user two things together in one message (keep it natural, not a form):

> "Let's start with the basics. What problem are you trying to solve — and what does success look like when it's done?"

**If the answer is vague:**

- Problem too generic (e.g., "improve performance") → ask: "Which part? How would you measure it?"
- Goal not measurable (e.g., "make it better") → ask: "Better compared to what? How will you know you've reached it?"

Clarify at most once per element. Do not loop.

---

### Round 2 — Scope

Ask in one message:

> "What's in scope for this change — and equally important, what's explicitly out of scope?"

**If the answer is vague:**

- Boundary unclear (e.g., "build the user page") → ask: "Does that include authentication? Profile settings? Notifications?"

Clarify at most once. Do not loop.

---

### Round 3 — Constraints & Acceptance Criteria

Ask in one message:

> "Last round: are there any constraints (tech stack, timeline, team, design decisions)? And what are the acceptance criteria — the specific conditions that must be true for this to be considered done?"

**If the answer is vague:**

- Constraints too open → ask for specifics (e.g., "must use existing auth system?")
- Acceptance criteria not verifiable → ask: "How would you verify that condition is met?"

Clarify at most once per element. Do not loop.

---

## Draft Brief

After collecting all answers, produce a draft in this exact format:

```markdown
# Brief: <change name in title case>

## Problem

<what problem is being solved and why it matters>

## Goal

<what success looks like, measurable outcome>

## Scope

### In scope

- <item>

### Out of scope

- <item>

## Constraints

- <constraint>

## Acceptance Criteria

- [ ] <verifiable condition>
```

Display the full draft to the user and use the **AskUserQuestion tool** to ask:

> "Here's the brief draft. Would you like to change anything before I save it?"

Offer options: `["Save as-is", "Edit something"]`

- If "Save as-is": proceed to save
- If "Edit something": ask what to change, update the brief, show the updated draft, ask again

---

## Saving the Brief

**Determine the change name** from the interview context (derive a kebab-case name from the problem/goal, e.g., "add user authentication" → `add-user-auth`). Use **AskUserQuestion tool** to confirm:

> "I'll save this brief as `openspec/changes/<derived-name>/brief.md`. Does that name work, or would you like a different one?"

Options: `["Yes, use that name", "Use a different name"]`

- If different name: ask for the name

**Create the change directory if needed:**

```bash
openspec new change "<name>"
```

If the change already exists (directory present), skip this step.

Save path: `openspec/changes/<name>/brief.md`

**If `brief.md` already exists at that path**, use **AskUserQuestion tool**:

> "A brief already exists for `<name>`. What would you like to do?"

Options: `["Overwrite it", "Use a different change name"]`

After saving:

1. Confirm: "Saved to `openspec/changes/<name>/brief.md`"
2. Suggest next step: "Run `/propose` to turn this brief into a full proposal with design and tasks."

---

## Guardrails

- Never skip a round — all 5 elements (problem, goal, scope, constraints, AC) must be collected
- Never save without showing the draft and getting explicit approval via AskUserQuestion
- Maximum 1 clarification round per information group — do not interrogate
- Keep questions conversational, not form-like — group related questions together
- Do not invent information not provided by the user
- If the user wants to stop mid-interview, save what's been collected so far as a partial draft and note which sections are incomplete

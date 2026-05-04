---
name: propose
description: Propose a new change with reviewable planning artifacts. Use when the user wants to quickly describe what they want to build and get proposal, specs, design, and tasks ready for design/test review before implementation.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.3.0"
---

Propose a new change - create the change and generate all artifacts in one step.

I'll create a change with artifacts:

- proposal.md (what & why)
- specs/**/*.md (requirements and scenarios)
- design.md (how)
- tasks.md (implementation steps)

After this, decide whether `/design <name>` is required. Low-risk changes may skip the `/design` review step and move to `/tdd <name>`, but never move directly to implementation.

---

**Input**: The user's request should include a change name (kebab-case) OR a description of what they want to build.

**Steps**

1. **If no clear input provided, ask what they want to build**

   Use the **AskUserQuestion tool** (open-ended, no preset options) to ask:

   > "What change do you want to work on? Describe what you want to build or fix."

   From their description, derive a kebab-case name (e.g., "add user authentication" → `add-user-auth`).

   **IMPORTANT**: Do NOT proceed without understanding what the user wants to build.

2. **Create the change directory**

   If the change doesn't already exist:
   ```bash
   openspec new change "<name>"
   ```

   If it does exist (e.g., user ran `/brief` first), skip this step.

3. **Load brief.md if available**

   Check if `openspec/changes/<name>/brief.md` exists. If yes, read it — use its content as additional context when creating all artifacts (problem, goal, scope, constraints, acceptance criteria). Do NOT mention this to the user unless relevant.

4. **Discover existing spec context**

   Before creating or updating any planning artifact, run Spec Context Discovery
   using the user request and `brief.md` if present.

   a. **Extract lightweight search terms**
   Identify terms from:
   - domain nouns (e.g., auth, billing, workflow)
   - user or system actions (e.g., login, export, validate)
   - entities (e.g., credential, issue, proposal)
   - UI/API/workflow surfaces (e.g., login screen, `/propose`, endpoint names)
   - error, validation, security, or trust-boundary terms
   - likely capability names

   b. **Search active specs first**
   Search `openspec/specs/**` with the extracted terms. Prefer `rg` when
   available. Read every matched active spec file that appears relevant before
   deciding whether a capability is new or modified.

   c. **Search active changes next**
   Search `openspec/changes/**` with the extracted terms, excluding
   `openspec/changes/archive/**`. Read related active change artifacts when they
   may overlap with the requested scope. Treat the current change separately so
   it is not reported as a conflict with itself.

   d. **Clarify ambiguous overlap**
   If another active change overlaps the same domain, capability, or
   requirements, ask whether to continue the existing change, merge scope, or
   proceed with the new change. Do not silently create conflicting planning
   artifacts.

   e. **Search archive only when needed**
   Do not search `openspec/changes/archive/**` by default. Search archived
   changes only when active specs and active changes are insufficient,
   conflicting, or missing rationale needed for a safe proposal. If archive
   context influences the artifacts, record why it was needed.

   f. **Use discovery for artifact generation**
   Use discovered context to decide:
   - existing capability vs new capability
   - `ADDED`, `MODIFIED`, `REMOVED`, or `RENAMED` delta operation
   - whether human clarification is needed before writing artifacts

   Record concise discovery evidence in generated artifacts, preferably in
   `proposal.md` under `Existing Context Reviewed`: related active specs,
   related active changes, and short impact notes. Do not copy full spec content.

5. **Get the artifact build order**

   ```bash
   openspec status --change "<name>" --json
   ```

   Parse the JSON to get:
   - artifact IDs and dependency order
   - `artifacts`: list of all artifacts with their status and dependencies

6. **Create planning artifacts in sequence**

   Use the **TodoWrite tool** to track progress through the artifacts.

   Loop through artifacts in dependency order (artifacts with no pending dependencies first), but only create the planning package:
   - `proposal`
   - `specs`
   - `design`
   - `tasks`

   Do not create `tests` here. `/tdd` owns test generation and human approval.

   a. **For each artifact that is `ready` (dependencies satisfied)**:
   - Get instructions:
     ```bash
     openspec instructions <artifact-id> --change "<name>" --json
     ```
   - The instructions JSON includes:
     - `context`: Project background (constraints for you - do NOT include in output)
     - `rules`: Artifact-specific rules (constraints for you - do NOT include in output)
     - `template`: The structure to use for your output file
     - `instruction`: Schema-specific guidance for this artifact type
     - `outputPath`: Where to write the artifact
     - `dependencies`: Completed artifacts to read for context
   - Read any completed dependency files for context
   - Create the artifact file using `template` as the structure
   - Apply `context` and `rules` as constraints - but do NOT copy them into the file
   - Show brief progress: "Created <artifact-id>"

   b. **Continue until the planning package is complete**
   - After creating each artifact, re-run `openspec status --change "<name>" --json`
   - Check if `proposal`, `specs`, `design`, and `tasks` have `status: "done"` in the artifacts array
   - Stop when those planning artifacts are done

   c. **If an artifact requires user input** (unclear context):
   - Use **AskUserQuestion tool** to clarify
   - Then continue with creation

7. **Show final status**
   ```bash
   openspec status --change "<name>"
   ```

8. **Decide the next handoff**

   Evaluate whether the change needs the `/design` review step.

   `/design` is required when any of these are true:
   - architecture, module boundaries, or dependency direction changes
   - data flow, state, API, schema, or contract changes
   - auth, authorization, validation, sensitive data, or trust boundary changes
   - new dependency, migration, data model, or rollback complexity
   - performance, concurrency, scale, or cost risk
   - requirements are still ambiguous or trade-offs are unresolved
   - implementation touches multiple modules or has unclear blast radius

   `/design` may be skipped when all of these are true:
   - change is docs-only, test-only, text/label rename, narrow config, or a very small bug fix
   - scope and affected files are obvious from proposal/specs/tasks
   - generated `design.md` has no unresolved decisions that need review
   - human explicitly accepts skipping the `/design` review step

   If skipping `/design`, say why and route to `/tdd <name>`.
   Do not route to `/apply` from `/propose`.

**Output**

After completing all artifacts, summarize:

- Change name and location
- List of artifacts created with brief descriptions
- What's ready: "Planning artifacts created. Ready for design/test review."
- Prompt:
  - If design review is required: "Run `/design <name>` to review/refine the technical design."
  - If design review can be skipped: "Design review can be skipped because <reason>. Run `/tdd <name>` next."
  - "After design review is approved or explicitly skipped, run `/tdd <name>` to create test scenarios or test files."
  - "Do not run implementation yet."

**Artifact Creation Guidelines**

- Follow the `instruction` field from `openspec instructions` for each artifact type
- The schema defines what each artifact should contain - follow it
- Run Spec Context Discovery before creating or updating planning artifacts
- Use discovered active specs and active changes for capability selection and delta operation choice
- Record related specs and active changes concisely; do not paste full matched specs into artifacts
- Read dependency artifacts for context before creating new ones
- Use `template` as the structure for your output file - fill in its sections
- **IMPORTANT**: `context` and `rules` are constraints for YOU, not content for the file
  - Do NOT copy `<context>`, `<rules>`, `<project_context>` blocks into the artifact
  - These guide what you write, but should never appear in the output

**Guardrails**

- Create the planning package only: proposal, specs, design, tasks
- Do not suggest `/apply` directly after `/propose`
- Always decide and explain whether `/design` review is required or can be skipped
- Do not create tests from `/propose`; use `/tdd`
- Do not generate planning artifacts before searching relevant active specs and active changes
- Do not search archived changes by default; use archive only for conflict or rationale gaps
- Always read dependency artifacts before creating a new one
- If context is critically unclear, ask the user - but prefer making reasonable decisions to keep momentum
- If a change with that name already exists, ask if user wants to continue it or create a new one
- Verify each artifact file exists after writing before proceeding to next

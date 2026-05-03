## 1. Review Gate Definition

- [x] 1.1 Define the required `review.md` machine metadata: `verdict`, `blocking_findings`, `reviewed_ref`, `reviewed_diff`, and `reviewed_at`.
- [x] 1.2 Define the required `review.md` sections: Findings, Tests Run, Residual Risk, and Verdict.
- [x] 1.3 Define accepted values: `verdict: pass|fail` and `blocking_findings: none|present`.
- [x] 1.4 Define fail-closed behavior for missing, malformed, duplicate, contradictory, or unknown review metadata.
- [x] 1.5 Define stale review behavior using `reviewed_ref` and `reviewed_diff`.
- [x] 1.6 Define explicit documented legacy bypass behavior for pre-gate changes.
- [x] 1.7 Document that `/review` compares implementation against proposal, specs, design, tasks, and tests when available.

## 2. Workflow Skill Updates

- [x] 2.1 Update `/apply` guidance so completed implementation hands off to `/review` before archive.
- [x] 2.2 Add `/review` skill guidance so `/review` writes `openspec/changes/<name>/review.md` with required metadata and sections.
- [x] 2.3 Update `/archive` guidance so review gate failures are hard blockers, not warnings.
- [x] 2.4 Update active workflow docs so command flow reads `/apply -> /review -> /archive`.

## 3. Archive Enforcement

- [x] 3.1 Block archive when `review.md` is missing for an applied change.
- [x] 3.2 Block archive when `review.md` metadata is malformed or unparseable.
- [x] 3.3 Block archive when `review.md` verdict is not `pass`.
- [x] 3.4 Block archive when `review.md` reports `blocking_findings: present`.
- [x] 3.5 Block archive when `review.md` is stale compared with current implementation state.
- [x] 3.6 Present review verdict and residual risk before final archive action.
- [x] 3.7 Ensure archive failure messages state the exact missing or failing review condition.

## 4. Verification

- [x] 4.1 Verify the documented workflow reads as `/apply -> /review -> /archive`.
- [x] 4.2 Verify a change without `review.md` cannot be archived.
- [x] 4.3 Verify a malformed `review.md` cannot be archived.
- [x] 4.4 Verify a change with non-pass verdict cannot be archived.
- [x] 4.5 Verify a change with blocking findings cannot be archived.
- [x] 4.6 Verify a stale review cannot be archived.
- [x] 4.7 Verify a change with pass verdict, no blocking findings, and current review state can proceed to remaining archive checks.

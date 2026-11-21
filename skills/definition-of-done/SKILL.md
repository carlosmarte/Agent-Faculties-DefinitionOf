---
name: definition-of-done
description: Verifies completed implementation work against strict Agile Definition of Done (DoD) quality, testing, regression, security, and documentation criteria before calling a job finished. Eliminates premature completion claims and rework by requiring empirical execution evidence. Use after code changes and before final handoff, commit, or pull request creation.
license: Apache-2.0
compatibility: Requires Bash/Shell execution for running test suites, linters, and build tools, plus Git and a pre-authenticated gh CLI for version control operations.
---

# Definition of Done (DoD) Gate

## Purpose

Act as a strict post-implementation verification gate. Writing code is not finishing the job. You MUST NOT declare a task complete, open a pull request, or hand work back as finished until you have empirically verified that the solution works, meets all acceptance criteria, introduces no regressions, and leaves the repository clean.

Those reliability checks rarely show up in a surface demo, but they are mandatory before calling any task **Done**.

## Entry Point

- **Before implementation (preferred):** Run [`INTERVIEW.md`](INTERVIEW.md) to agree on the done criteria with the user. It writes `.definition-of-x/<slug>/done.md` with `status: DEFINED`.
- **After implementation:** If `done.md` exists, verify against its *Agreed Done Criteria* (Phases 1–3 below). If it does not exist, run the interview in compressed form first, then verify.
- **Driven by the [`definition-of-x`](../definition-of-x/SKILL.md) orchestrator?** Follow its handoff.

---

## Execution Workflow

### Phase 1: Self-Audit & Workspace Hygiene
Inspect `git status` and `git diff` across all modified and untracked files:
1. **Scope Alignment:** Confirm every changed line directly serves the agreed Purpose and Scope. Revert unrelated formatting churn or accidental edits.
2. **Artifact Cleanup:** Remove temporary debug statements (`console.log`, `print`, `dbg!`), commented-out dead code, scratch scripts, and mock stubs introduced during development.
3. **Secret & Config Safety:** Confirm zero hardcoded API keys, tokens, credentials, or machine-specific local paths exist in the diff.

### Phase 2: Empirical Verification Criteria
Verify the work against all six mandatory completion gates. **Reading your own code does not count as verification**—you must execute deterministic checks whenever an execution environment is available.

#### 1. Acceptance Criteria & Edge Cases Met
- **Check:** Map every requirement and material edge case from `done.md` *Agreed Done Criteria* (or, failing that, the Definition of Ready brief or original request) to concrete behavior and test coverage, including any *Team-Specific Criteria*.
- **Requirement:** Zero silent omissions, `TODO` placeholders, or unimplemented fallback branches unless explicitly agreed out-of-scope.

#### 2. Automated Tests Pass (New + Existing)
- **Check:** Run the project's unit, integration, or end-to-end test suite covering affected modules.
- **Requirement:** New or updated behavior includes corresponding automated tests, and the test command exits with code `0`.

#### 3. Build, Typecheck & Static Analysis Clean
- **Check:** Execute the repository's native build, typechecker, linter, and formatter checks (e.g., `npm run build`, `tsc --noEmit`, `pytest`, `ruff check`, `cargo check`, `go test ./...`).
- **Requirement:** Zero new compiler errors, type errors, or linter violations.

#### 4. Regression & Integration Safety
- **Check:** Verify downstream callers, public exports/barrels, database migrations, and configuration schemas remain backward-compatible (or follow the approved migration plan).
- **Requirement:** Existing functionality outside the target feature continues to pass verification.

#### 5. Documentation & Operational Readiness
- **Check:** Inspect `README.md`, inline docstrings, `.env.example`, API schemas, and runbooks/changelogs.
- **Requirement:** Any changed CLI flag, environment variable, public API signature, or setup step is accurately documented alongside the code.

#### 6. Reproducible Verification Evidence
- **Check:** Capture the exact commands executed and their concise pass/fail outputs so a human reviewer can reproduce your verification immediately.

---

### Phase 3: Gate Evaluation & Remediation Loop

#### If ANY Criterion Fails (`NOT DONE`)
1. **Do NOT claim the task is complete.**
2. Diagnose the root cause of the failing test, build error, or unverified edge case.
3. Fix the defect and re-run **all** affected Phase 2 checks from scratch (fixing a linter error can break a test; re-verify end-to-end).
4. If an external environment limitation prevents running a check (e.g., missing database container or sandbox restriction), explicitly mark status as `PARTIALLY VERIFIED — ENVIRONMENT BLOCKED` and provide exact commands for the user to run locally. Never fabricate test output.

#### If ALL Criteria Pass (`DONE`)
Generate a structured **Definition of Done Receipt** in your final handoff response (and PR body, if opening a PR), and persist it to the artifact (see *Artifact* below):

```text
STATUS: DONE
1. Summary of Changes:
   - <Concise summary of modified files and delivered outcome>

2. Acceptance Criteria Verification:
   - [x] <Criterion 1> -> Verified via <test name / command>
   - [x] <Edge Case 1> -> Verified via <test name / command>

3. Quality & Regression Gates:
   - Build / Typecheck: PASS (`<exact command run>`)
   - Lint / Format: PASS (`<exact command run>`)
   - Test Suite: PASS (`<exact command run>` — <X passed, 0 failed>)
   - Workspace Hygiene: Clean (`git status` verified, no secrets or debug artifacts)

4. Documentation & Follow-Ups:
   - Docs Updated: <Files updated, or "None required — internal logic only">
   - Residual Risks / Next Slice: <Any deferred out-of-scope items>
```

---

## Artifact

Persist every gate outcome (`DONE`, `NOT DONE`, or `PARTIALLY VERIFIED — ENVIRONMENT BLOCKED`):

- **Location:** `.definition-of-x/<work-item-slug>/done.md` at the root of the target repository, the same file the interview created.
- **Template:** [`assets/done.template.md`](assets/done.template.md). Fill in *Part 2 — Verification Receipt* (and *Blockers* when not done), set `status:`, and bump `updated:`. Never edit *Part 1* during verification. If the criteria must change, go back to the user.
- **Re-runs:** Overwrite *Part 2* in place on every remediation loop, so the file always reflects the latest verification.
- **Hygiene:** `.definition-of-x/` is an intentional, reviewable artifact. Do not treat it as stray output during Phase 1 cleanup.

---

## Operational Guardrails

- **Zero Hallucinated Verification:** Never claim tests, builds, or linters passed unless you actually executed the command in the current state of the workspace and observed exit code `0`.
- **No Silent Scope Cuts:** Never silently drop a hard requirement to make the build pass. Surface blockers transparently to the user.
- **Native CLI Enforcement:** When committing verified work or opening a pull request, you MUST use standard `git` commands and the native GitHub CLI (`gh pr create`, `gh issue comment`).
- **Forbidden Tools:** You are explicitly forbidden from using the `github-create_pull_request` MCP tool or any other MCP mutation connectors under any circumstances.

---

## Related Faculties

- **[`definition-of-ready`](../../definition-of-ready/SKILL.md):** The pre-implementation counterpart. Its `ready.md` seeds this faculty's interview. *Scope Boundaries*, *Acceptance Criteria*, and *Verification Plan* flow into `done.md` Part 1.
- **[`definition-of-x`](../definition-of-x/SKILL.md):** The orchestrator that runs the Definition of Ready first, then this gate.

---
faculty: definition-of-ready
work_item: <slug>
source: <issue URL / spec path / "chat request">
status: <READY | NOT READY | SPIKE REQUIRED>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Definition of Ready — <Work Item Title>

## Verdict

STATUS: <READY | NOT READY | SPIKE REQUIRED>

## Readiness Gates

| # | Gate | Result | Evidence / Gap |
|---|---|---|---|
| 1 | Purpose is Clear | <PASS / FAIL> | <...> |
| 2 | Scope is Understood | <PASS / FAIL> | <...> |
| 3 | Acceptance Criteria are Testable | <PASS / FAIL> | <...> |
| 4 | Dependencies are Accounted For | <PASS / FAIL> | <...> |
| 5 | Work Fits the Execution Horizon | <PASS / FAIL> | <...> |

<!-- Keep exactly ONE of the three sections below, matching the verdict. -->

## Readiness Brief (READY)

- **Purpose & Outcome:** <1–2 sentences on why it matters and the expected result>
- **Scope Boundaries:**
  - In scope: <...>
  - Out of scope: <...>
- **Acceptance Criteria:**
  - [ ] AC1: <measurable behavior> (verified by `<test / command>`)
  - [ ] AC2: <...>
- **Edge Cases:**
  - [ ] EC1: <...> (verified by `<test / command>`)
- **Verification Plan:** `<exact test commands>`
- **Dependencies:** <verified tools, files, and contracts>
- **Practical First Step:** <immediate concrete action>

## Missing Readiness Criteria (NOT READY)

1. **[<Criterion Name>]:** <specific gap>
   - Why it matters: <impact on implementation or testing>
   - Proposed Default / Action: <safe fallback or required input>
   - Assigned To: <owner>

**Recommended Split (if oversized):**
- Slice 1: <independently verifiable outcome>
- Slice 2: <independently verifiable outcome>

## Spike Proposal (SPIKE REQUIRED)

- **Core Question:** <exact technical or product question>
- **Time / Execution Box:** <strict cap>
- **Allowed Scope:** <read-only analysis or isolated throwaway branch>
- **Concrete Deliverable:** <recommendation, ADR draft, benchmark, PoC>
- **Exit Path:** <how findings unblock DoR for the follow-up item>

## Interview Log

| Round | Question | Answer (verbatim or "default accepted") |
|---|---|---|
| 0 | <...> | <...> |

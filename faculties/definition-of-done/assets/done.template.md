---
faculty: definition-of-done
work_item: <slug>
ready_artifact: <./ready.md | none>
status: <DEFINED | NOT DONE | PARTIALLY VERIFIED — ENVIRONMENT BLOCKED | DONE>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# Definition of Done — <Work Item Title>

<!-- Part 1 is written by INTERVIEW.md before implementation (status: DEFINED).
     Part 2 is written by the gate in SKILL.md after implementation. -->

## Part 1 — Agreed Done Criteria

### Acceptance Criteria & Edge Cases
- [ ] AC1: <measurable behavior> (check: `<test / command>`)
- [ ] EC1: <edge case> (check: `<test / command>`)

### Quality Gates
| Gate | Command | Required |
|---|---|---|
| Tests | `<command>` | exit 0, new tests for each AC |
| Build / Typecheck | `<command>` | exit 0 |
| Lint / Format | `<command>` | zero new violations |

### Regression Boundaries
- Must stay compatible: <callers, exports, schemas, configs>
- Breaking changes allowed: <no | yes, with migration plan: ...>

### Documentation
- Must update if affected: <README, .env.example, API schema, CHANGELOG, ...>

### Delivery
- <uncommitted | commit on branch `<name>` | PR via `gh pr create`>

### Team-Specific Criteria
- [ ] <extra criterion> (check: <how>) — or "None"

---

## Part 2 — Verification Receipt

<!-- Filled by the gate. Re-runs overwrite this section and bump `updated:`. -->

```text
STATUS: <DONE | NOT DONE | PARTIALLY VERIFIED — ENVIRONMENT BLOCKED>
1. Summary of Changes:
   - <files modified and delivered outcome>

2. Acceptance Criteria Verification:
   - [x] <AC1> -> Verified via <test name / command>
   - [x] <EC1> -> Verified via <test name / command>

3. Quality & Regression Gates:
   - Build / Typecheck: <PASS | FAIL> (`<exact command run>`)
   - Lint / Format: <PASS | FAIL> (`<exact command run>`)
   - Test Suite: <PASS | FAIL> (`<exact command run>` — <X passed, Y failed>)
   - Workspace Hygiene: <Clean | Issues> (`git status` verified, no secrets or debug artifacts)

4. Documentation & Follow-Ups:
   - Docs Updated: <files, or "None required — internal logic only">
   - Residual Risks / Next Slice: <deferred out-of-scope items>
```

### Blockers (if not DONE)
- <failing criterion> — <root cause> — <command for the user to run locally, if environment blocked>

---

## Interview Log

| Round | Question | Answer (verbatim or "default accepted") |
|---|---|---|
| 0 | <...> | <...> |

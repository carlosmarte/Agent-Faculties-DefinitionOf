# Definition of Done — Interview (Entry Point)

This is the **first interaction** with the user about what "done" means for a work item. Run it **before implementation starts** (ideally right after the Definition of Ready passes), so both sides agree on the finish line up front. Its output is `done.md` with `status: DEFINED`. The gate in [`SKILL.md`](SKILL.md) verifies against that file after implementation.

If implementation already happened and no `done.md` exists, run this interview anyway in compressed form (one round), then go straight to the gate.

---

## Interview Rules

1. **Seed from Ready.** If `.definition-of-x/<slug>/ready.md` exists, import its Acceptance Criteria, Edge Cases, Scope Boundaries, and Verification Plan, and do **not** re-ask them. Only ask the user to confirm them.
2. **Detect, then confirm.** Discover the project's real quality commands from manifests and CI config (`package.json` scripts, `Makefile`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `.github/workflows/*`). Propose them, and never ask the user to type a command you can find yourself.
3. **Rounds, not walls.** Ask at most **4 questions per round**, each with a default. Use a structured question tool (e.g., `AskUserQuestion`) when available.
4. **Stop early.** End once every Phase 2 criterion in SKILL.md has a concrete, executable check or an explicit "not applicable" reason.
5. **Record verbatim decisions** in the artifact's *Interview Log*.

---

## Round 0 — Intake (always)

> "Let's agree on what 'done' means before we build, so verification is a checklist rather than a judgment call. I'll save it to `.definition-of-x/<slug>/done.md`."

| # | Question | Default |
|---|---|---|
| 0.1 | Which work item? | Slug from `ready.md`, or derived from the request |
| 0.2 | Confirm the acceptance criteria and edge cases (shown, imported from `ready.md`). Anything to add or remove? | "Confirmed as listed" |

---

## Round 1 — Quality Gates (Criteria 2–3)

| Criterion | Question | Example default |
|---|---|---|
| 2. Tests | Which test command proves it, and what new tests are expected? | Detected `npm test` / `pytest` / `go test ./...`, plus one test per AC |
| 3. Build / Typecheck | Which build and typecheck commands must pass? | Detected `npm run build`, `tsc --noEmit`, `cargo check` |
| 3. Lint / Format | Which lint and format checks must pass? | Detected `ruff check`, `eslint .`, `gofmt -l` |
| 2–3. CI parity | Must the local checks mirror CI exactly? | "Yes: run the same commands CI runs" |

---

## Round 2 — Safety & Docs (Criteria 4–5)

| Criterion | Question | Example default |
|---|---|---|
| 4. Regression | Which downstream callers, public APIs, migrations, or config schemas must stay backward compatible? | Exports/callers of touched modules, found via search |
| 4. Regression | Is a breaking change allowed? If so, what is the migration plan? | "No breaking changes" |
| 5. Docs | Which docs must change if behavior changes? (README, `.env.example`, API schema, CHANGELOG, runbook) | Docs that reference touched flags, env vars, or APIs |

---

## Round 3 — Handoff Policy (Criterion 6 + delivery)

| Question | Example default |
|---|---|
| How should verified work be delivered? (leave uncommitted / commit on branch / open PR) | "Commit on a feature branch, no PR" |
| Any team-specific done criteria? (reviewer sign-off, screenshot, perf benchmark, changelog entry, ticket link) | "None beyond the defaults" |

Delivery always uses native `git` / `gh` (see SKILL.md *Operational Guardrails*). Any team-specific criteria the user adds become extra rows in the artifact's checklist and are verified like any other criterion.

---

## Handoff

1. Write `.definition-of-x/<slug>/done.md` from [`assets/done.template.md`](assets/done.template.md) with `status: DEFINED`. Fill in **Agreed Done Criteria** and leave **Verification Receipt** empty.
2. Show the user the checklist and the artifact path.
3. After implementation, run the gate ([`SKILL.md`](SKILL.md) Phases 1–3) against this file.

# Definition of Ready — Interview (Entry Point)

This is the **first interaction** with the user when a work item has no `ready.md` artifact yet. Its job is to gather just enough information to score the five readiness gates in [`SKILL.md`](SKILL.md), then hand off to the gate evaluation.

The interview is a conversation, not a form. Keep it short and well-informed, and anchor every question to a gate.

---

## Interview Rules

1. **Inspect before you ask.** Do the read-only context inspection (SKILL.md, Phase 1) *first*. Never ask the user something the repository, issue, or spec already answers. Instead, state what you found and ask them to confirm it.
2. **Rounds, not walls.** Ask at most **4 questions per round**. Prefer a structured question tool (e.g., `AskUserQuestion`) when the harness provides one, otherwise use a numbered list.
3. **Always offer a default.** Every question carries a proposed answer derived from context, so the user can reply "yes" or "default" to fast-track.
4. **Stop early.** End the interview once all five gates can be scored. Do not ask optional questions for completeness' sake.
5. **Record verbatim decisions.** Capture user answers in the artifact's *Interview Log* so the reasoning is auditable.
6. **Never start implementation** during the interview.

---

## Round 0 — Intake (always)

Open with one sentence that states what you will do, e.g.:

> "Before we build anything, I'll run a quick Definition of Ready check: a few questions, then a readiness verdict saved to `.definition-of-x/<slug>/ready.md`."

Establish:

| # | Question | Default if unanswered |
|---|---|---|
| 0.1 | What is the work item? (Paste the request, issue URL/number, or spec path.) | The current user request |
| 0.2 | Short name for this work item? | Kebab-case slug derived from the title, e.g. `add-csv-export` |

If an issue URL or number is given, read it with `gh issue view <n>` (read-only).

---

## Round 1 — Purpose & Scope (Gates 1–2)

Ask only the questions whose answers are missing after inspection:

| Gate | Question | Example default |
|---|---|---|
| 1. Purpose | What problem does this solve, and for whom (user, team, system component)? | "Users of `<module>` cannot `<X>`" |
| 1. Purpose | What is the observable outcome when it works? | Inferred behavior from the request |
| 2. Scope | What is explicitly **out of scope** for this pass? | "No UI changes; no schema migrations" |
| 2. Scope | Any conflicting sources or constraints (style guides, existing patterns, deadlines)? | "Follow existing patterns in `<dir>`" |

---

## Round 2 — Verification (Gate 3)

Turn subjective goals into measurable assertions. For each acceptance criterion, propose a concrete test:

| Question | Example default |
|---|---|
| How will we know it works? List the acceptance criteria. | Drafted from Round 1, ready for confirmation |
| Which edge cases or error states matter? (empty input, auth failure, timeouts, limits) | Top 2–3 inferred from code paths |
| Is there a quantitative target? ("fast" → "p95 < 200 ms on fixture X") | "No perf target" |
| Which test command or runner proves it? | Detected runner, e.g. `npm test`, `pytest`, `go test ./...` |

If the user answers with a subjective metric, reflect it back as a measurable proposal and ask them to confirm it.

---

## Round 3 — Dependencies & Size (Gates 4–5)

| Gate | Question | Example default |
|---|---|---|
| 4. Dependencies | Is anything required that isn't available now? (credentials, env vars, API contracts, other team sign-off, schema approval) | "None detected" plus the list of what was verified |
| 4. Dependencies | For each open dependency: who owns it, and what is the fallback? | `User / Product Owner` |
| 5. Size | Should this ship in one pass, or should we slice it? | A proposed split if the item touches more than one subsystem |
| 5. Size | What is the very first concrete step? | e.g. "Add failing test for `<case>` in `<file>`" |

Skip any row already answered by inspection.

---

## Handoff to the Gate

When the interview ends:

1. Score all five gates using [`SKILL.md`](SKILL.md) Phase 2.
2. Route to Path A / B / C (SKILL.md Phase 3).
3. Write the artifact from [`assets/ready.template.md`](assets/ready.template.md) to `.definition-of-x/<slug>/ready.md` (see SKILL.md, *Artifact*).
4. Show the user the status line and the artifact path. If the status is `NOT READY`, ask only for the missing pieces and re-run the gate, which updates the same file.

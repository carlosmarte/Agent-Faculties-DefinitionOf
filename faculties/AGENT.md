# AGENT.md — Definition of X (DoX)

Always-on guidance for agents working in a project that uses the DoX faculties. It explains **what DoX is, when to use it, and the rules that always apply**. The detailed procedures live in the skills. Load them on demand rather than restating them here.

## What DoX Is

DoX wraps every unit of work in two Agile quality gates, each backed by a persistent artifact:

| Gate | Question | Runs | Artifact |
|---|---|---|---|
| **Definition of Ready (DoR)** | Is this work clear, testable, feasible, and sized for one pass? | Before writing code | `.definition-of-x/<slug>/ready.md` |
| **Definition of Done (DoD)** | Is this work empirically verified, regression-safe, documented, and clean? | Criteria agreed before building, verified after | `.definition-of-x/<slug>/done.md` |

```text
 "DoR or DoD?" ─▶ DoR interview ─▶ ready.md ─▶ DoD interview ─▶ done.md (DEFINED) ─▶ build ─▶ DoD gate ─▶ done.md (DONE)
```

## Faculties

| Skill | Use it when | Entry point |
|---|---|---|
| `definition-of-x` | Getting started, or unsure which gate applies. Asks the user **DoR or DoD?** and routes. | `definition-of-x/SKILL.md` |
| `definition-of-ready` | A new request or issue is about to be implemented | `definition-of-ready/INTERVIEW.md` → `SKILL.md` |
| `definition-of-done` | Defining the finish line, or about to claim work is complete, commit, or open a PR | `definition-of-done/INTERVIEW.md` → `SKILL.md` |

## When To Trigger

- **Suggest DoR** before implementing any request that is vague, cross-cutting, has subjective success criteria ("make it faster"), or would take more than a trivial edit. For a one-line fix with an obvious test, skip it.
- **Always apply DoD** before saying "done", committing, or opening a PR. That includes running the real test, build, and lint commands.
- **Resume, don't restart.** If `.definition-of-x/` exists in the repo, read the `status:` frontmatter of the relevant work item and continue from that stage.
- If the user explicitly declines a gate, respect that. Note it once in the handoff, then don't nag.

## Status Vocabulary

| Artifact | Statuses |
|---|---|
| `ready.md` | `READY` · `NOT READY` · `SPIKE REQUIRED` |
| `done.md` | `DEFINED` · `NOT DONE` · `PARTIALLY VERIFIED — ENVIRONMENT BLOCKED` · `DONE` |

Only a gate may set these. The orchestrator and the agent's own judgment never mark work `READY` or `DONE`.

## Interview Conduct

- Inspect the repository (read-only) **before** asking anything. Never ask what the code, manifests, CI config, or issue already answer.
- Ask in rounds of **at most 4 questions**, each with a proposed default. Use a structured question tool when available.
- Stop as soon as the gate criteria can be scored, and record the answers in the artifact's *Interview Log*.

## Non-Negotiable Rules

1. **No speculative coding.** Do not write production code while DoR is `NOT READY`. Spikes stay isolated and time-boxed.
2. **No hallucinated verification.** Claim a check passed only if you ran the command in the current workspace and saw exit code `0`. If the environment blocks a check, use `PARTIALLY VERIFIED — ENVIRONMENT BLOCKED` and give the user the exact commands.
3. **No silent scope cuts.** Scope changes go back to the user and are recorded in `ready.md` and `done.md` Part 1 before work continues.
4. **Artifacts are the source of truth.** Update files in place (bump `updated:`) and never rely on conversation memory for status.
5. **Native CLI only for GitHub.** Use `git` and `gh` (`gh issue create`, `gh pr create`). Never use MCP mutation connectors such as `github-create_pull_request`.
6. **Commit only on request.** Commit or push only when the user asks, or when `done.md` *Delivery* records that they agreed to it.

## Further Reading

- Installation for Claude Code, Gemini CLI, and GitHub Copilot: [`INSTALL.md`](INSTALL.md)
- Overview and artifact layout: [`README.md`](README.md)

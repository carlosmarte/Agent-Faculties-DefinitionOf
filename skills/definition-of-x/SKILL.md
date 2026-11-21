---
name: definition-of-x
description: Orchestrates the Agile Definition of Ready and Definition of Done gates for a work item. Starts by asking the user which one they want (DoR or DoD), then interviews them to define readiness and completion criteria, and produces two persistent artifacts (ready.md and done.md) under .definition-of-x/<work-item>/. Use when starting new work, when a user asks how to get started with DoR/DoD, or to resume or verify a tracked work item.
license: Apache-2.0
compatibility: Requires filesystem read/write access to the target repository and Bash/Shell execution for the Definition of Done gate. Git and a pre-authenticated gh CLI are needed for issue intake, commits, and pull requests.
---

# Definition of X — Orchestrator

## Purpose

Guide the user from "I have a request" to "verified and handed off" through two quality gates, with a persistent artifact at each step:

```text
 [0] "What do you want to do?"
   │
   ├── Definition of Ready ──▶ [1]
   └── Definition of Done ───▶ [2] or [4] (decided by done.md status)

 [1] Ready interview ──▶ ready.md   (READY | NOT READY | SPIKE REQUIRED)
   │ READY → offer to continue to Done
   ▼
 [2] Done interview ───▶ done.md    (status: DEFINED, the agreed finish line)
   │
   ▼
 [3] Implementation (outside this faculty)
   │
   ▼
 [4] Done gate ────────▶ done.md    (DONE | NOT DONE | PARTIALLY VERIFIED)
```

This faculty owns **sequencing, the getting-started conversation, and artifact bookkeeping**. The gates themselves live in:

- [`definition-of-ready`](../../definition-of-ready/SKILL.md) (entry: [`INTERVIEW.md`](../../definition-of-ready/INTERVIEW.md))
- [`definition-of-done`](../definition-of-done/SKILL.md) (entry: [`INTERVIEW.md`](../definition-of-done/INTERVIEW.md))

---

## Artifacts

Each work item gets one folder in the target repository:

```text
.definition-of-x/
└── <work-item-slug>/
    ├── ready.md   ← Definition of Ready verdict + brief / gaps / spike
    └── done.md    ← Part 1: agreed criteria · Part 2: verification receipt
```

- **Slug:** lowercase kebab-case, at most 50 characters, derived from the work item title (e.g. `add-csv-export`). If an issue exists, prefix its number (`142-add-csv-export`).
- **Stage is derived from the files.** Never keep separate state elsewhere:

| `ready.md` | `done.md` | Current stage → next action |
|---|---|---|
| missing | — | **[1]** Run the Ready interview |
| `NOT READY` / `SPIKE REQUIRED` | — | **[1]** Resolve gaps or run the spike, then re-run the Ready gate |
| `READY` | missing | **[2]** Run the Done interview |
| `READY` | `DEFINED` | **[3]** Implement, then **[4]** |
| `READY` | `NOT DONE` / `PARTIALLY VERIFIED…` | **[4]** Remediate and re-run the Done gate |
| `READY` | `DONE` | Complete. Offer delivery per `done.md` *Delivery* |

---

## Step 0 — Ask What the User Wants To Do

The **very first thing** the user sees is one question with exactly two options. Do not greet at length, explain the flow, or ask anything else first. Use a structured question tool (e.g., `AskUserQuestion`) when the harness provides one, otherwise a numbered list:

> **What do you want to do?**
> 1. **Definition of Ready (DoR):** check that a work item is clear, testable, and sized right *before* building.
> 2. **Definition of Done (DoD):** agree on what "finished" means, or verify finished work *after* building.

If the user's opening request already names one (e.g., "run DoD on this"), treat the question as answered and skip it.

Only **after** the choice:

1. **Detect context (read-only):** the repo root (`git rev-parse --show-toplevel`) and the existing `.definition-of-x/*/` folders with their `status:` values.
2. **Pick the work item:** if folders exist, ask "New work item, or one of these?" and list each slug with its stage (see the stage table). Otherwise treat it as new and let the chosen interview's Round 0 capture it.
3. **Route:**

   | Choice | Condition | Go to |
   |---|---|---|
   | DoR | — | **Step 1** |
   | DoD | `done.md` missing | **Step 2** (if `ready.md` is missing too, say so in one line, recommend DoR first, and proceed if the user still wants DoD) |
   | DoD | `done.md` is `DEFINED` | Ask: "Has the work been implemented?" Yes → **Step 4**. No → show the agreed criteria and stop |
   | DoD | `done.md` is `NOT DONE` / `PARTIALLY VERIFIED…` | **Step 4** (re-verify) |
   | DoD | `done.md` is `DONE` | Report the receipt. Offer re-verification or delivery |

**Pacing:** default to *Guided* (pause for approval after each artifact). If the user says "fast-track" or "accept defaults" at any point, accept proposed defaults and pause only on `NOT READY` / `NOT DONE`.

---

## Step 1 — Definition of Ready

Follow [`definition-of-ready/INTERVIEW.md`](../../definition-of-ready/INTERVIEW.md), then its gate.

- **READY:** Show the brief and the `ready.md` path, then ask "Continue to the Definition of Done?" If yes, go to Step 2. If no, end with the Final Summary.
- **NOT READY:** Present the gaps with their owners and defaults. Offer three choices: answer the gaps now (re-run the gate), **accept all proposed defaults** (re-run the gate), or stop here with the artifact saved as the backlog record.
- **SPIKE REQUIRED:** Present the spike proposal. Run it only with explicit user authorization and within its time box. The spike's findings feed a re-run of the Ready gate.

Never chain from Step 1 into Step 2 unless `ready.md` says `READY`. (A user who picks DoD directly in Step 0 is routed by that table instead.)

## Step 2 — Definition of Done (criteria)

Follow [`definition-of-done/INTERVIEW.md`](../definition-of-done/INTERVIEW.md). It imports acceptance criteria from `ready.md`, so this round should be mostly confirmations. Result: `done.md` with `status: DEFINED`.

Then present a two-line summary of both artifacts and the practical first step from `ready.md`.

## Step 3 — Implementation

Out of scope for this faculty. Either the agent proceeds (if the user asked it to build) or the user builds. Respect `ready.md` *Scope Boundaries* throughout. If scope must change, update `ready.md` and `done.md` Part 1 with the user's agreement **before** continuing.

## Step 4 — Definition of Done (verification)

Run the [`definition-of-done`](../definition-of-done/SKILL.md) gate against `done.md`. It fills in Part 2 (the receipt) and loops on failures. On `DONE`, carry out delivery exactly as recorded in `done.md` *Delivery*, and in any case only with the user's go-ahead.

---

## Final Summary

When the flow ends (at any stage), finish with:

```text
DEFINITION OF X — <slug>
- Ready: <status>  →  .definition-of-x/<slug>/ready.md
- Done:  <status>  →  .definition-of-x/<slug>/done.md
- Next:  <single concrete next action, or "Complete">
```

---

## Operational Guardrails

- **Gates are authoritative.** This orchestrator never overrides a gate verdict, and never marks `READY` or `DONE` itself.
- **One question round at a time.** Never dump all interview rounds at once. Follow each interview's rules (inspect first, at most 4 questions, always offer defaults).
- **Artifacts are the source of truth.** Read status from files on resume. Do not rely on conversation memory.
- **Committing artifacts:** `.definition-of-x/` is meant to be versioned alongside the work. Commit or push it only when the user asks, or when `done.md` *Delivery* says to.
- **Native CLI Enforcement & Forbidden Tools:** Inherit both gates' guardrails. Use native `git` / `gh` only, and never use MCP mutation connectors such as `github-create_pull_request`.

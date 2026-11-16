---
name: definition-of-ready
description: Evaluates incoming work items, feature requests, and prompts against strict Agile Definition of Ready (DoR) criteria before implementation begins. Identifies missing context, untestable acceptance criteria, unaccounted dependencies, and oversized scope, turning open feasibility questions into time-boxed investigations. Use before writing code or starting any build task.
license: Apache-2.0
compatibility: Requires filesystem read access and reasoning capabilities. Git and a pre-authenticated gh CLI are required if logging readiness gaps or investigation issues to GitHub.
---

# Definition of Ready (DoR) Gate

## Purpose

Act as a pre-implementation readiness gate. Before writing production code, modifying system state, or starting a build, evaluate whether the work item is **clear, testable, and feasible**.

A work item is **Ready** only when you can clearly explain:
1. **Why it matters** (the problem or opportunity and who it affects).
2. **What outcome is expected** (concrete behavior and boundaries).
3. **How you will verify it** (deterministic tests and edge-case checks).
4. **What the practical first step is** (an immediate, unambiguous action).
5. **Whether the work fits within a single execution pass / sprint** without guesswork or context exhaustion.

Do **not** begin implementation on vague, untestable, or unbounded requests. Stopping early prevents wasted tokens, architectural drift, and downstream rework.

## Entry Point

- **No `ready.md` artifact yet for this work item?** Start with [`INTERVIEW.md`](INTERVIEW.md). It is the first interaction with the user and gathers the inputs for Phases 1–2.
- **Artifact already exists?** Read it, re-score only the gates that were `FAIL` (or that the user says have changed), and update the same file.
- **Driven by the [`definition-of-x`](../definition-of-x/SKILL.md) orchestrator?** It handles slugs and sequencing. Follow its handoff.

---

## Execution Workflow

### Phase 1: Context Inspection
1. Read the user's request, issue description, or specification.
2. Perform lightweight, **read-only** inspection of the relevant codebase, manifests, test runners, and documentation to see if implicit questions are already answered by existing repository conventions.
3. Never mutate application files during a Definition of Ready check. The only permitted write is the artifact described below.

### Phase 2: Evaluate Readiness Criteria
Score the work item against all five mandatory readiness gates:

#### 1. Purpose is Clear
- **Check:** Does the request state the problem or opportunity, identify who or what system component it affects, and define the expected outcome?
- **Failure Signal:** Request asks for an arbitrary code change without explaining the intended behavior or end-user/system impact, leaving architectural trade-offs ambiguous.

#### 2. Scope is Understood
- **Check:** Are in-scope and out-of-scope boundaries explicit? Do you have enough repository and domain context to make implementation decisions without inventing requirements?
- **Failure Signal:** Vague directives (e.g., "clean up the API", "make onboarding better") or conflicting source requirements.

#### 3. Acceptance Criteria are Testable
- **Check:** Can you deterministically verify the expected behavior, including edge cases and error states that materially affect the outcome?
- **Failure Signal:** Subjective success metrics (e.g., "make it fast", "handle errors gracefully") with no measurable assertion, expected status code, or test target.

#### 4. Dependencies are Accounted For
- **Check:** Are required upstream decisions, target files, schemas, credentials, CLI tools, and inputs from external systems available right now? If any dependency remains open, does it have a clear owner and mitigation plan?
- **Failure Signal:** Blocked by missing API contracts, unavailable environment variables, unapproved breaking schema changes, or external team sign-off.

#### 5. Work Fits the Sprint / Execution Horizon
- **Check:** Can the item be reasonably completed and verified within the current sprint or bounded agent session? Can you name a concrete, practical first step?
- **Failure Signal:** Multi-epic initiatives bundled into a single task that cannot be verified incrementally.

---

### Phase 3: Decision Routing

Route the task into one of three deterministic states based on Phase 2:

#### Path A: `READY` (All 5 Criteria Met)
Output a concise **Readiness Brief** and proceed to implementation (or await confirmation if operating in plan-only mode):
```text
STATUS: READY
- Purpose & Outcome: <1-2 sentences on why it matters and expected result>
- Scope Boundaries: <In-scope vs. out-of-scope summary>
- Verification Plan: <Exact test commands, assertions, and material edge cases>
- Dependencies: <Verified available tools/files/contracts>
- Practical First Step: <Immediate concrete action>
```

#### Path B: `NOT READY — GAPS DETECTED` (Missing Information or Oversized Scope)
**Halt implementation immediately.** Document what is missing so the user or designated owner can resolve it before work enters execution:
1. List every failed criterion and the exact missing detail.
2. For each ambiguity, state **why it matters** and propose a sensible **default assumption** if the user wishes to fast-track approval.
3. If the work item is too large for a single pass, propose a concrete split into smaller, independently verifiable work items.
4. Assign each open gap to an explicit owner (e.g., `User / Product Owner`, `Platform Team`, or `Agent Spike`).

```text
STATUS: NOT READY
Missing Readiness Criteria:
1. [<Criterion Name>]: <Specific gap>
   - Why it matters: <Impact on implementation or testing>
   - Proposed Default / Action: <Safe fallback or required input>
   - Assigned To: <Owner>

Recommended Split (if oversized):
- Slice 1: <Independently verifiable outcome>
- Slice 2: <Independently verifiable outcome>
```

#### Path C: `INVESTIGATION / SPIKE REQUIRED` (Open Feasibility Question)
If the work item itself—or a critical blocker—is an open question (e.g., *"Is third-party SDK X compatible with our runtime?"* or *"Why is endpoint Y timing out under load?"*), do **not** commit to building the feature. Instead, define and propose (or execute, if authorized) a **time-boxed investigation**:
```text
STATUS: SPIKE REQUIRED
- Core Question: <Exact technical or product question to answer>
- Time / Execution Box: <Strict cap, e.g., 30 minutes human time OR max 10 read-only agent steps>
- Allowed Scope: <Read-only analysis or isolated throwaway prototype branch>
- Concrete Deliverable: <Written recommendation, ADR draft, benchmark table, or throwaway PoC>
- Exit Path: <How findings will unblock DoR for the follow-up implementation ticket>
```

---

## Artifact

Persist the outcome of every evaluation, regardless of path:

- **Location:** `.definition-of-x/<work-item-slug>/ready.md` at the root of the target repository (create the directories if needed).
- **Template:** [`assets/ready.template.md`](assets/ready.template.md). Keep only the section that matches the verdict, and fill in the *Interview Log*.
- **Re-evaluation:** Update the same file in place. Bump `updated:` and `status:` rather than creating a new file.
- **Chat output:** Still print the `STATUS:` block from Phase 3 in your response, followed by the artifact path.

---

## Operational Guardrails

- **No Speculative Coding:** Never write production feature code to "see what sticks" when Purpose, Scope, or Acceptance Criteria fail DoR.
- **Throwaway Prototypes Stay Isolated:** If executing an authorized feasibility spike, keep prototype code strictly isolated from main feature branches until the spike deliverable is reviewed.
- **Native CLI Enforcement:** When logging missing dependencies, creating split backlog items, or opening spike tickets on GitHub, you MUST use native `git` and the pre-authenticated `gh` CLI (e.g., `gh issue create`, `gh issue edit`, `gh pr create`).
- **Forbidden Tools:** You are strictly forbidden from using `github-create_pull_request` or any other MCP mutation connectors under any circumstances.

---

## Related Faculties

- **[`definition-of-done`](../definition-of-done/SKILL.md):** The post-implementation counterpart. The Readiness Brief's *Acceptance Criteria*, *Edge Cases*, and *Verification Plan* seed `done.md`, which the Definition of Done gate must empirically verify before the work is declared complete.
- **[`definition-of-x`](../definition-of-x/SKILL.md):** The orchestrator that runs this gate first, then the Definition of Done.

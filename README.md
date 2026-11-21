# Faculties

Faculties are agent harness capabilities: self-contained skills an agent loads to change how it plans, executes, or verifies work. Each faculty lives in its own directory with a `SKILL.md`: YAML frontmatter with `name` and `description`, followed by Markdown instructions. The `name` field matches the directory name.

## Definition of X

The *Definition of X* faculties are Agile quality gates that wrap every unit of work. Start with the orchestrator, **`definition-of-x`**. It first asks the user what they want to do, **Definition of Ready** or **Definition of Done**, then runs that gate (and offers to continue from Ready into Done):

```text
  request ──▶ [ definition-of-ready ] ──▶ [ definition-of-done: define ] ──▶ implementation ──▶ [ definition-of-done: verify ] ──▶ handoff / PR
                 │  INTERVIEW.md → ready.md     │  INTERVIEW.md → done.md (DEFINED)                  │  SKILL.md → done.md receipt
                 ├─ READY                       └─ agreed finish line                                 ├─ DONE
                 ├─ NOT READY  → gaps, owners, split                                                  ├─ NOT DONE           → fix + re-verify
                 └─ SPIKE REQUIRED → time-boxed investigation                                         └─ PARTIALLY VERIFIED → env-blocked
```

| Faculty | Entry point | When | Artifact |
| --- | --- | --- | --- |
| [`definition-of-x`](skills/definition-of-x/SKILL.md) | `SKILL.md` | Getting started, resuming, or verifying a work item | Orchestrates the two below |
| [`definition-of-ready`](definition-of-ready/SKILL.md) | [`INTERVIEW.md`](definition-of-ready/INTERVIEW.md) | Before writing code | `.definition-of-x/<slug>/ready.md` |
| [`definition-of-done`](skills/definition-of-done/SKILL.md) | [`INTERVIEW.md`](skills/definition-of-done/INTERVIEW.md) | Criteria before building, verification after | `.definition-of-x/<slug>/done.md` |

Each gate's `INTERVIEW.md` is the first interaction with the user. It inspects the repo first, asks short rounds of at most 4 questions with proposed defaults, and hands off to the gate in `SKILL.md`. Artifact templates live in each faculty's `assets/`.

### Artifacts

Artifacts are written into the *target* repository, one folder per work item:

```text
.definition-of-x/<work-item-slug>/
├── ready.md   # verdict, gate table, brief / gaps / spike, interview log
└── done.md    # Part 1: agreed done criteria (before build) · Part 2: verification receipt (after build)
```

The `status:` frontmatter in these files is the single source of truth, so a work item can be resumed in a later session.

## Installation

See [`INSTALL.md`](INSTALL.md) for Claude Code, Gemini CLI, and GitHub Copilot. Agent-facing guidance that you can load into every session is in [`AGENT.md`](AGENT.md). Quick start for Claude Code:

```sh
# Claude Code (user-level)
ln -s "$PWD/skills/definition-of-x" ~/.claude/skills/definition-of-x
ln -s "$PWD/definition-of-ready" ~/.claude/skills/definition-of-ready
ln -s "$PWD/skills/definition-of-done" ~/.claude/skills/definition-of-done
```

## Adding a faculty

1. Create `<faculty-name>/SKILL.md` (lowercase, hyphen-separated name).
2. Set frontmatter `name` to the directory name and write a `description` (1024 characters or fewer) that says what the faculty does and when to use it.
3. Optionally add an `INTERVIEW.md` entry point and `assets/` templates for any artifact it produces.
4. Add a row to the table above.

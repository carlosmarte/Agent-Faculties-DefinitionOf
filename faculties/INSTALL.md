# Installing the Definition of X (DoX) Faculties

DoX ships as three skill folders. Each folder contains a `SKILL.md` plus supporting files (`INTERVIEW.md`, `assets/`), so **always install the whole folder**, never just `SKILL.md`.

| Faculty | Role |
|---|---|
| `definition-of-x` | Orchestrator and entry point. Asks "DoR or DoD?" and routes. |
| `definition-of-ready` | Pre-implementation gate. Writes `ready.md`. |
| `definition-of-done` | Done criteria and post-implementation gate. Writes `done.md`. |

Install **all three**. The orchestrator links to the other two by relative path (`../definition-of-ready/...`), so they must be siblings in the same skills directory.

In the commands below, set `FACULTIES` to this folder:

```sh
FACULTIES=/path/to/definition-of-x/faculties
SKILLS=(definition-of-x definition-of-ready definition-of-done)
```

**Symlink or copy?** A symlink keeps you on the latest version automatically. A copy is safer if your harness or sandbox does not follow symlinks, and it is required for project installs that teammates clone.

---

## Claude Code

| Scope | Location |
|---|---|
| User (all projects) | `~/.claude/skills/<name>/` |
| Project (shared via git) | `<repo>/.claude/skills/<name>/` |

```sh
# User scope (symlink)
mkdir -p ~/.claude/skills
for s in "${SKILLS[@]}"; do ln -sfn "$FACULTIES/$s" ~/.claude/skills/$s; done

# Project scope (copy, then commit)
mkdir -p .claude/skills
for s in "${SKILLS[@]}"; do cp -R "$FACULTIES/$s" .claude/skills/; done
```

**Verify:** Start a new session and run `/definition-of-x`, or ask "Run the definition of ready on this task". The skill should load and ask *DoR or DoD?*

---

## Gemini CLI

| Scope | Location |
|---|---|
| User | `~/.gemini/skills/<name>/` (alias: `~/.agents/skills/`) |
| Workspace | `<repo>/.gemini/skills/<name>/` (alias: `.agents/skills/`) |

```sh
# Option A: built-in linker (creates symlinks in ~/.gemini/skills)
gemini skills link "$FACULTIES"

# Option B: built-in installer (user scope by default; add --scope workspace for the project)
gemini skills install "$FACULTIES"

# Option C: manual
mkdir -p ~/.gemini/skills
for s in "${SKILLS[@]}"; do ln -sfn "$FACULTIES/$s" ~/.gemini/skills/$s; done
```

**Verify:** Inside a session, run `/skills reload` and then `/skills list`. All three names should appear. Then ask "Start definition of x".

> The `gemini skills` subcommands and flags have changed across releases. If a command fails, check `gemini skills --help` and fall back to Option C.

---

## GitHub Copilot

Skills work in the **Copilot CLI**, the **Copilot coding agent**, and **agent mode** in VS Code and Visual Studio.

| Scope | Location | Applies to |
|---|---|---|
| Personal | `~/.copilot/skills/<name>/` (also reads `~/.claude/skills/`) | Copilot CLI, coding agent |
| Repository | `<repo>/.github/skills/<name>/` | CLI, coding agent, IDE agent mode |

```sh
# Personal scope (symlink)
mkdir -p ~/.copilot/skills
for s in "${SKILLS[@]}"; do ln -sfn "$FACULTIES/$s" ~/.copilot/skills/$s; done

# Repository scope (copy, then commit; required for the cloud coding agent)
mkdir -p .github/skills
for s in "${SKILLS[@]}"; do cp -R "$FACULTIES/$s" .github/skills/; done
```

**Verify:** Skills are discovered at session start, so open a **new** Copilot CLI session or agent-mode chat. Then ask "Use definition-of-x to get started" (in the CLI, `/skills` lists what is loaded).

> The cloud coding agent only sees what is committed to the repository, so use the repository scope for it.

---

## Optional: Always-On Context (`AGENT.md`)

Skills load on demand when the request matches their description. To make every session aware of DoX, so the agent proposes a readiness check before coding and a done check before handoff, reference [`AGENT.md`](AGENT.md) from your harness's context file:

| Harness | Context file | Add |
|---|---|---|
| Claude Code | `CLAUDE.md` (project) or `~/.claude/CLAUDE.md` | `@/path/to/faculties/AGENT.md` |
| Gemini CLI | `GEMINI.md` (project) or `~/.gemini/GEMINI.md` | `@/path/to/faculties/AGENT.md` |
| Copilot | `.github/copilot-instructions.md` or `AGENTS.md` | Paste the contents of `AGENT.md` (no import syntax) |

---

## Version Control for Artifacts

DoX writes artifacts to `.definition-of-x/<work-item>/` in the repository being worked on. Commit them alongside the work (recommended), or add `.definition-of-x/` to `.gitignore` if you want them local only.

## Uninstall

```sh
for d in ~/.claude/skills ~/.gemini/skills ~/.copilot/skills .claude/skills .gemini/skills .github/skills; do
  for s in "${SKILLS[@]}"; do rm -rf "$d/$s"; done
done
```

Check what will be removed first (`ls -la <dir>`). For symlinks, `rm -rf` removes only the link, not the source faculty.

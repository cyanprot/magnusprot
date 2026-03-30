# magnusprot

Personal skill suite for Claude Code — workflow orchestration, TDD-first development, systematic debugging, multi-perspective code review, and system utilities.

## Install

**Claude Code plugin (official):**

```bash
# 1. Register marketplace
/plugin marketplace add cyanprot/magnusprot

# 2. Install plugin
/plugin install magnusprot@magnusprot
```

**Local (no install):**

```bash
claude --plugin-dir ~/magnusprot
```

## Skills (29)

Skills are namespaced as `/magnusprot:<skill-name>` when installed as a plugin.

### Core Workflow Chain

`skills-sys` → `brainstorm-flow` → `plan-flow` → `plancheck-flow` → `execute-flow` → `verify-anly`

Supporting: `implement-flow`, `review-anly`, `worktree-flow`, `finish-dev`

### Analysis & Review

| Skill | Description |
|-------|-------------|
| `review-anly` | Multi-perspective code review (7-perspective framework) |
| `explain-anly` | Code structure explanation |
| `simplify-anly` | Complexity audit (YAGNI, DRY, coupling) |
| `perf-anly` | Performance analysis |
| `trace-anly` | Execution path tracing |
| `respond-anly` | Response quality evaluation |
| `receive-review` | Code review evaluation & response |

### Development

| Skill | Description |
|-------|-------------|
| `init-dev` | Project context loading |
| `commit-dev` | Conventional Commits |
| `clean-dev` | System cleanup (Fedora + Ubuntu/Pop!_OS) |
| `finish-dev` | Branch completion workflow |
| `deps-dev` | Dependency management |
| `docs-dev` | Documentation generation |
| `security-dev` | Security audit |
| `healthcheck-dev` | Project health verification |
| `llmcheck-dev` | LLM output validation |

### System & Utilities

| Skill | Description |
|-------|-------------|
| `debug-anly` | Root cause analysis before fixing |
| `parallel-sys` | Parallel agent dispatch |
| `research-sys` | API/library investigation |
| `session-handoff` | Session context wrap-up |
| `skilldev-sys` | Skill creation & testing |

## Agents (8)

| Agent | Description |
|-------|-------------|
| `code-reviewer` | Plan-aligned review with 7-perspective framework |
| `code-architect` | System design evaluation and structural trade-offs |
| `code-explorer` | Deep codebase exploration and execution path tracing |
| `silent-failure-hunter` | Finds bugs that don't throw errors |
| `test-strategist` | Test design — edge cases, coverage gaps, test architecture |
| `migration-planner` | Version upgrades and API migration planning |
| `incident-investigator` | Error chain tracing for loud failures (complements silent-failure-hunter) |
| `spec-validator` | Requirements compliance validation against specs/PRDs |

## Multi-distro Support

`clean-dev` detects the package manager at runtime (`dnf`/`apt`) and adapts commands accordingly. Includes northprot (Pop!_OS) safety features: NVIDIA/GPU package protection, sudo policy detection, generic-hwe kernel protection.

## License

MIT

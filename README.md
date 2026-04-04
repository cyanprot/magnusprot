# magnusprot

Personal skill suite for Claude Code — workflow orchestration, TDD-first development, systematic debugging, multi-perspective code review, and system utilities.

---

## Install

**Claude Code plugin (marketplace):**

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

---

## Architecture

```mermaid
graph TD
    A[skills-sys] --> B[brainstorm-flow]
    B --> C[plan-flow]
    C --> D[plancheck-flow]
    D --> E[execute-flow]
    E --> F[verify-anly]
    F --> G[worktree-flow]

    E -->|per task| H[implement-flow]
    E -->|post-sprint| I[evaluator agent]
    H --> J[review-anly]

    subgraph Agents
        I
        K[code-reviewer]
        L[code-architect]
        M[code-explorer]
        N[test-strategist]
        O[spec-validator]
        P[debugger agents]
        Q[migration-planner]
    end

    J --> K
```

---

## Skills (28)

Skills are namespaced as `/magnusprot:<skill-name>` when installed as a plugin.

### Core Workflow Chain

`skills-sys` &rarr; `brainstorm-flow` &rarr; `plan-flow` &rarr; `plancheck-flow` &rarr; `execute-flow` &rarr; `verify-anly`

Supporting: `implement-flow`, `review-anly`, `worktree-flow`, `finish-dev`

### Analysis & Review

| Skill | Description |
|-------|-------------|
| `review-anly` | Multi-perspective code review (7-perspective framework) |
| `explain-anly` | Code structure explanation |
| `simplify-anly` | Complexity audit (YAGNI, DRY, coupling) |
| `perf-anly` | Performance analysis |
| `trace-anly` | Execution path tracing |
| `respond-anly` | Code review feedback response — technical verification |
| `verify-anly` | Plan compliance and execution evidence gate |

### Development

| Skill | Description |
|-------|-------------|
| `init-dev` | Project context loading and environment diagnosis |
| `commit-dev` | Conventional Commits format |
| `clean-dev` | System cleanup (multi-distro: apt/dnf) |
| `finish-dev` | Branch completion (delegates to worktree-flow Phase 2) |
| `deps-dev` | Dependency status and security audit |
| `docs-dev` | Documentation generation and audit |
| `security-dev` | Security vulnerability scanning |
| `healthcheck-dev` | Project health verification (build, runtime, docs) |
| `llmcheck-dev` | LLM model settings validation |

### Workflow

| Skill | Description |
|-------|-------------|
| `brainstorm-flow` | Requirements exploration and design shaping |
| `plan-flow` | Step-by-step TDD plan + campaign/sprint contracts |
| `plancheck-flow` | Plan review gate — catch over-engineering and scope creep |
| `execute-flow` | Plan execution — batch or subagent mode with two-stage review |
| `implement-flow` | TDD-first feature implementation |
| `worktree-flow` | Git worktree lifecycle (isolated feature work) |

### System & Utilities

| Skill | Description |
|-------|-------------|
| `debug-anly` | Root cause analysis before fixing |
| `parallel-sys` | Parallel agent dispatch for independent tasks |
| `research-sys` | API/library investigation with structured findings |
| `session-handoff` | Session context wrap-up and persistence |
| `skilldev-sys` | Skill creation, editing, and testing |
| `skills-sys` | Skill discovery and usage guidance |

---

## Agents (9)

| Agent | Description |
|-------|-------------|
| `code-reviewer` | Plan-aligned review with 7-perspective framework |
| `code-architect` | System design evaluation and structural trade-offs |
| `code-explorer` | Deep codebase exploration and execution path tracing |
| `evaluator` | Black-box live-app testing via Playwright (4-phase: happy path, edge cases, mobile, regression) |
| `silent-failure-hunter` | Finds bugs that don't throw errors — empty results, missing data, swallowed exceptions |
| `incident-investigator` | Error chain tracing for loud failures — stack traces, crash analysis |
| `test-strategist` | Test design — edge cases, coverage gaps, test architecture |
| `migration-planner` | Version upgrades and API deprecation response |
| `spec-validator` | Requirements compliance validation against specs/PRDs |

---

## Campaign System

magnusprot supports multi-session project campaigns via `.claude/campaign.json`:

- **plan-flow** generates campaign state + sprint contracts
- **execute-flow** resumes campaigns across sessions (reads `continuation_prompt`)
- **verify-anly** validates sprint contract compliance (Gate 3)
- **evaluator** agent runs Playwright black-box testing post-sprint

Sprint contracts are stored in `.claude/sprint-contracts/<project>-<N>.json`.

---

## Multi-distro Support

`clean-dev` detects the package manager at runtime (`dnf`/`apt`) and adapts commands accordingly. Includes Pop!_OS safety features: NVIDIA/GPU package protection, sudo policy detection, generic-hwe kernel protection.

---

## License

[MIT](LICENSE)

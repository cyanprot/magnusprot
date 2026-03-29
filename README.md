# magnusprot

Personal skill suite for Claude Code. Workflow orchestration, TDD-first development, systematic debugging, multi-perspective code review, and system utilities.

## Install

```bash
npx skills add cyan-ide/magnusprot -y -g
```

## Skills (27)

### Core Workflow Chain
`skills-sys` → `brainstorm-flow` → `plan-flow` → `plancheck-flow` → `execute-flow` → `verify-anly`

Supporting: `implement-flow`, `review-anly`, `worktree-flow`, `finish-dev`

### Standalone
- `debug-anly` — Root cause analysis before fixing
- `parallel-sys` — Parallel agent dispatch
- `research-sys` — API/library investigation
- `explain-anly` — Code structure explanation
- `init-dev` — Project context loading
- `clean-dev` — System cleanup (Fedora + Ubuntu)
- `commit-dev` — Conventional Commits
- `session-handoff` — Session wrap-up
- `skilldev-sys` — Skill creation & testing
- `receive-review` — Code review evaluation
- `healthcheck-dev` — Project health verification
- `simplify-anly` — Complexity audit
- `deps-dev`, `docs-dev`, `llmcheck-dev`, `perf-anly`, `respond-anly`, `security-dev`, `trace-anly`

## Agents (1)
- `code-reviewer` — Multi-perspective code review agent

## License
MIT

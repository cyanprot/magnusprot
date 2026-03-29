---
name: implement-flow
description: Use when implementing features or fixing bugs — TDD-first approach
user-invocable: true
argument-hint: "[task description or plan file path]"
---

## Role

You are an **Implementer** agent. You write production code using strict TDD methodology: tests first, implementation second, self-review always.

## Project Context Detection

Before starting, detect the project environment:

1. **READ** the current working directory structure
2. **IDENTIFY** the language and framework:
   - `Cargo.toml` -> Rust (use `cargo test`)
   - `pyproject.toml` / `setup.py` -> Python (use `pytest` or `uv run pytest`)
   - `package.json` -> Node.js/TypeScript (use `npm test` or `vitest`)
   - `go.mod` -> Go (use `go test`)
3. **FIND** existing test patterns in the project (test directory, naming conventions, fixtures)
4. **CHECK** for design docs or implementation plans in `docs/plans/`
5. **CHECK** for project rules in `.claude/rules/`

## Session Protocol

Follow this sequence exactly. Do not skip steps.

1. **READ** the task specification (provided as $ARGUMENTS)
2. **READ** existing related files -- imports, interfaces, patterns already in the project
3. **RED** -- Write failing tests
4. **VERIFY RED** -- Run tests and confirm they FAIL
5. **GREEN** -- Write minimal implementation to make tests pass
6. **VERIFY GREEN** -- Run tests and confirm they PASS
7. **REFACTOR** -- Clean up if needed, verify tests still pass
8. **SELF-REVIEW** -- Run through the checklist below
9. **FULL SUITE** -- Run the complete test suite to ensure no regressions
10. **GIT COMMIT** -- Stage and commit your changes

## TDD Workflow

**REQUIRED:** Read `references/tdd-methodology.md` for full TDD methodology (rationalizations, examples, verification checklist).

Follow the RED-GREEN-REFACTOR cycle strictly:
1. **RED** — Write failing test (one behavior per test)
2. **VERIFY RED** — Run test, confirm it fails for expected reason
3. **GREEN** — Write minimal code to pass
4. **VERIFY GREEN** — Run test, confirm all pass
5. **REFACTOR** — Clean up, verify tests still pass

**Iron Law:** No production code without a failing test first. Code before test? Delete it.

**Testing anti-patterns:** See `references/testing-anti-patterns.md` before adding mocks or test utilities.

## Self-Review Checklist

Before committing, verify each item. Fix violations immediately.

### Design
- [ ] No circular imports/dependencies between modules
- [ ] Single Responsibility -- each class/function does one thing
- [ ] No function exceeds 80 lines
- [ ] No file exceeds 400 lines
- [ ] No god objects or classes doing everything

### Readability
- [ ] Max 3 levels of nesting (use early returns)
- [ ] Clear, descriptive naming (no single-letter vars except loop counters)
- [ ] No magic numbers -- use constants or config
- [ ] Type annotations on all public functions and methods

### Reliability
- [ ] No bare exception catching -- always catch specific errors
- [ ] Edge cases handled (empty input, null/None, boundary values)
- [ ] Async code is race-condition free (no shared mutable state without locks)
- [ ] Resources are properly cleaned up

### Cleanliness
- [ ] No unused imports
- [ ] No commented-out code
- [ ] No copy-paste duplication
- [ ] No debug print statements (use logging if needed)
- [ ] No hardcoded secrets, URLs, or credentials

## Error Handling Rules

- If tests fail after implementation: **fix the code, not the tests** (unless tests have a genuine bug)
- If blocked by a missing dependency or unclear interface: **report what's needed** -- do not guess or stub critical behavior
- If the task spec is ambiguous: **implement the most conservative interpretation** and note the ambiguity in a code comment

## Git Protocol

After self-review passes and all tests are green:

```bash
# Stage ONLY the files you created or modified
git add <specific files>

# Commit with conventional format
git commit -m "$(cat <<'EOF'
feat(<scope>): <short description>

<optional body explaining what and why>

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

- Use conventional commit types: `feat`, `fix`, `refactor`, `test`, `docs`
- Stage specific files -- NEVER use `git add .` or `git add -A`
- NEVER push to remote -- only commit locally

## Important Constraints

- Do NOT modify existing tests from previous tasks (unless they have a genuine bug)
- Do NOT add docstrings or comments to code you didn't write
- Keep solutions simple -- avoid over-engineering for hypothetical future needs
- When in doubt, look at existing code for patterns to follow

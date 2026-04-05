---
name: commit-dev
description: Use when committing — auto-format as Conventional Commits
model: haiku
user-invocable: true
---

# /commit-conv - Conventional Commit

Commits using [Conventional Commits](https://www.conventionalcommits.org/) format.

## Commit Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | feat: add user login |
| `fix` | Bug fix | fix: handle null pointer exception |
| `docs` | Documentation change | docs: update API docs |
| `style` | Formatting (no behavior change) | style: fix indentation |
| `refactor` | Refactoring | refactor: remove duplicate code |
| `test` | Add/modify tests | test: add login tests |
| `chore` | Build, config changes | chore: update dependencies |
| `perf` | Performance improvement | perf: optimize query |
| `ci` | CI configuration | ci: add GitHub Actions |

## Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

### Example
```
feat(auth): add password reset

- Send reset link via email
- Token expires in 24 hours
- Password complexity validation

Closes #123
```

## Behavior

### 1. Check Changes
```bash
git status
git diff --staged
```

### 2. Analyze Changes
- What files were changed?
- Nature of the change? (feature, bug fix, refactoring, etc.)
- Related issues?

### 3. Determine Commit Type
Select the most appropriate type for the changes

### 4. Write Commit Message
- Title: Under 50 chars, imperative mood
- Body: Explain what and why

### 5. Execute Commit
```bash
git commit -m "$(cat <<'EOF'
<type>(<scope>): <description>

<body>

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

## Notes
- One logical change per commit
- Add `!` for Breaking Changes: `feat!: change API`
- Include related issue numbers in footer: `Closes #123`
- Unstaged files are not committed

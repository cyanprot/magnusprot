# QC Worker: Build & Test

Evaluate whether this project builds and runs correctly.
ZERO false positives. If a command fails, check whether YOU used the wrong command first.

**RULES:**
- Do NOT read source code. Do NOT grep through files.
- Only run commands a user would run.
- Report exactly what happened — stdout/stderr, exit codes.

---

## Pre-flight: Understand the project

Detect project type and available commands:

```bash
cd "$PROJECT_ROOT"
# Detect package manager
if [ -f pnpm-lock.yaml ]; then echo "PKG_MGR=pnpm"
elif [ -f yarn.lock ]; then echo "PKG_MGR=yarn"
elif [ -f Cargo.toml ]; then echo "PKG_MGR=cargo"
elif [ -f pyproject.toml ]; then echo "PKG_MGR=poetry/pip"
elif [ -f package-lock.json ]; then echo "PKG_MGR=npm"
else echo "PKG_MGR=unknown"; fi
```

For Node.js projects, read `package.json` scripts. Use EXACT script names — adding your own flags may cause errors that are YOUR mistake.

---

## Steps — Run each one. If a step fails, continue to the next.

### Step 1: Install dependencies
```bash
{PKG_MGR} install 2>&1 | tail -30
# pnpm: pnpm install --frozen-lockfile
# npm: npm ci
# yarn: yarn install --frozen-lockfile
# cargo: cargo fetch
# pip/poetry: pip install -e . / poetry install
```

### Step 2: Build
```bash
# Run the project's build script if it exists
{PKG_MGR} build 2>&1 | tail -40
# cargo: cargo build
# python: skip if no build step
```

### Step 3: Typecheck (if script exists)
```bash
{PKG_MGR} typecheck 2>&1 | tail -30
# or: {PKG_MGR} type-check / tsc --noEmit
# cargo: cargo check
# python: mypy / pyright if configured
```

### Step 4: Lint (if script exists)
```bash
{PKG_MGR} lint 2>&1 | tail -30
# cargo: cargo clippy
# python: ruff / flake8 if configured
```

### Step 5: Run tests
```bash
{PKG_MGR} test 2>&1 | tail -50
# cargo: cargo test
# python: pytest
```

---

## Self-Verification Protocol (MANDATORY for every FAIL)

Before reporting ANY finding as FAIL, answer ALL:

1. **Was my command correct?** Re-check manifest scripts. Did I add a flag not in the definition? If yes → DISCARD.
2. **Is this a known runtime behavior?** DEP0040, peer dep warnings, ExperimentalWarning → P3 max, NOT a bug.
3. **Is this a cascade failure?** If build failed (Step 2) → dist missing → later steps fail = 1 root issue. Only report root cause.
4. **Would a senior developer agree this is a real bug?** If they'd say "you used the wrong command" → DISCARD.

If ANY check fails → discard and explain in Self-Verification Notes.

### False Positive Indicators (auto-discard)
- `Unknown option` after YOUR added flag
- `DEP0040`, `DEP0060`, any DeprecationWarning → P3 max
- Peer dependency warnings → P3 max
- `ExperimentalWarning` → P3 max
- Exit code 124 from `timeout` → not a code bug
- Missing `dist/` when build already failed → cascade

---

## Report Format

```markdown
# QC Worker: Build & Test
## Cycle: {CYCLE_ID}

### Pre-flight
- Package manager: {detected}
- Scripts found: {list}

### Results
| Step | Command | Status | Exit Code |
|------|---------|--------|-----------|
| 1 | install | PASS/FAIL | 0/N |
| 2 | build | PASS/FAIL/SKIP | 0/N |
| 3 | typecheck | PASS/FAIL/SKIP | 0/N |
| 4 | lint | PASS/FAIL/SKIP | 0/N |
| 5 | test | PASS/FAIL/SKIP | 0/N |

### Findings
- [FINDING-B1] severity: P0/P1/P2/P3. What failed and error message.

### Self-Verification Notes
- Step N: {command} failed. (1) Command correct? {y/n} (2) Known behavior? {y/n} (3) Cascade? {y/n} (4) Senior agrees? {y/n}
- Verdict: REAL ISSUE / DISCARDED (reason)

### Output (failed commands only)
(last 10-15 lines of stderr/stdout for each FAIL)
```

## Severity
- P0: Install or build completely broken (real compilation errors, not warnings)
- P1: Tests fail with assertion errors
- P2: Lint errors, type errors that don't block build
- P3: Deprecation warnings, peer dep warnings, cosmetic

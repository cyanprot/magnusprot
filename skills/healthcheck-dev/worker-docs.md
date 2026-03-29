# QC Worker: Documentation Walkthrough

Test whether documentation is accurate and complete — NOT whether code works.

ZERO false positives. Code bug causing a documented command to fail = Worker A's problem, not yours.

**RULES:**
- Do NOT read source code. Do NOT analyze code patterns.
- READ the docs, then EXECUTE every command mentioned in them.
- Report: did the documented command actually work? Yes or no?
- Execute commands EXACTLY as documented. Do NOT add your own flags.

**WORKER BOUNDARY — NOT your responsibility:**
- Code bugs that cause documented commands to fail → Worker A
- Infrastructure issues (services down, DB unreachable) → Worker B
- You only report: "the docs say X, but X doesn't work because of a documentation error"

---

## Pre-flight: Understand the project

```bash
cd "$PROJECT_ROOT" && node -e "const p=require('./package.json'); console.log(JSON.stringify(p.scripts,null,2))" 2>/dev/null || echo "No package.json scripts"
```

Distinguish: script exists → run as documented vs. docs add flags the script already has → docs error.

---

## Steps

### Step 1: Read the README
Use the Read tool on `$PROJECT_ROOT/README.md`.

### Step 2: Read additional docs (if they exist)
Check for `CONTRIBUTING.md`, `docs/`, `operation.md`, or similar.

### Step 3: Follow Prerequisites
For each prerequisite mentioned, verify it's installed:
```bash
node --version 2>&1
{PKG_MGR} --version 2>&1
docker --version 2>&1
```

### Step 4: Follow Setup Instructions
Execute every command listed in the setup section, in order. For each:
- Run EXACTLY as documented
- Record: PASS or FAIL with actual output

### Step 5: Environment Setup
If docs say "copy .env.example to .env":
```bash
# Don't overwrite .env! Just check sync
diff <(grep -oP '^[A-Z_]+' "$PROJECT_ROOT/.env.example" 2>/dev/null | sort) \
     <(grep -oP '^[A-Z_]+' "$PROJECT_ROOT/.env" 2>/dev/null | sort) 2>&1
```
Report if .env.example and .env are out of sync.

### Step 6: Try every documented command
```bash
cd "$PROJECT_ROOT" && timeout 10 {command} 2>&1 | tail -15
```
Run EXACTLY as written in docs. Do NOT modify.

### Step 7: Check referenced files exist
For each file path mentioned in docs:
```bash
[ -e "$PROJECT_ROOT/{path}" ] && echo "EXISTS" || echo "MISSING: {path}"
```

---

## Self-Verification Protocol (MANDATORY for every FAIL)

Before reporting ANY finding as FAIL, answer ALL:

1. **Am I testing docs or code?** README command correct but code buggy → Worker A. DISCARD.
2. **Did I run exactly as documented?** Added my own flags → DISCARD (my mistake).
3. **Is infrastructure down?** Service/DB issues → Worker B. DISCARD.
4. **Is this a cascade?** Previous step failed and this depends on it → only report first failure.

If ANY check fails → discard and explain in Self-Verification Notes.

### What IS a documentation finding
- README says `pnpm start` but script is named `dev` → docs error
- README references `config/settings.yml` but file doesn't exist → docs error
- README lists Node.js 18 but project requires 22 → docs error
- Critical setup step missing → docs gap

### What is NOT a documentation finding
- README says `pnpm build` and command exists but fails with TypeError → code bug (Worker A)
- README says `docker compose up` but Docker daemon stopped → infra (Worker B)

---

## Report Format

```markdown
# QC Worker: Documentation Walkthrough
## Cycle: {CYCLE_ID}

### Pre-flight
- Scripts found: {list}

### README Commands Tested
| # | Documented Command | Worked? | Actual Result |
|---|-------------------|---------|---------------|
| 1 | {command} | YES/NO | ... |

### Referenced Files
| Path in Docs | Exists? |
|-------------|---------|
| {path} | YES/NO |

### Documentation Gaps
- Additional docs found: {list}
- Missing steps needed to get running: ...

### .env Sync
- .env.example vs .env: IN SYNC / OUT OF SYNC ({details})

### Findings
- [FINDING-D1] severity: P0/P1/P2/P3. Docs say "X" but actual is "Y".

### Self-Verification Notes
- Finding N: (1) Testing docs or code? {docs/code} (2) Ran exactly? {y/n} (3) Infra issue? {y/n} (4) Cascade? {y/n}
- Verdict: DOCS ISSUE / DISCARDED (reason)

### Output (failed commands only)
(actual error output)
```

## Severity
- P0: Following docs leads to a dead end — critical step missing or completely wrong
- P1: Wrong script name, referenced file doesn't exist
- P2: Instructions incomplete or ambiguous, .env.example out of sync
- P3: Minor wording issues, slightly outdated version numbers

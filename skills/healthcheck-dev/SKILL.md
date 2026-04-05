---
name: healthcheck-dev
description: Use when verifying project health — parallel build, runtime, and docs checks
model: sonnet
user-invocable: true
---


# Project QC

Parallel quality verification: **build**, **runtime**, **docs**. Zero false positives is priority #1.

**REQUIRES:** none

## Step 1: Auto-Detect Project

| Detect | How |
|--------|-----|
| Package manager | `pnpm-lock.yaml`→pnpm, `yarn.lock`→yarn, `Cargo.toml`→cargo, `pyproject.toml`→poetry/pip, `package-lock.json`→npm |
| Scripts | Read manifest scripts section |
| Services | `systemctl --user list-units --type=service --state=running` → fallback: `docker compose ps` |
| Docs | `README.md`, `docs/`, `CONTRIBUTING.md` |
| Entry point | manifest `main` field or `start`/`dev` script |
| Database | Parse `DATABASE_URL` or `POSTGRES_*`/`MYSQL_*` from `.env` |

## Step 2: Dispatch 3 Workers in Parallel

| Worker | Template | Scope |
|--------|----------|-------|
| Build | @worker-build.md | Install, build, typecheck, lint, test |
| Runtime | @worker-runtime.md | Services, DB, cache, app startup, health |
| Docs | @worker-docs.md | README accuracy, referenced files, env sync |

Pass each: `PROJECT_ROOT` (cwd), `PKG_MGR`, `SCRIPTS`, `CYCLE_ID` (timestamp `YYYYMMDD_HHmm`).

## Step 3: False Positive Screening

**Auto-reject:** (1) Worker used wrong command/flag (2) Cascade: downstream failures from single root cause (3) Deprecation warnings (DEP0040, ExperimentalWarning) above P3 (4) Missing artifacts when build already failed

**Downgrade:** Lint/format P0/P1→P2 | Informational warning P0/P1→P3 | Peer dep P1+→P3 | Config issues P0→P2

## Step 4: Cascade Detection

Extract `[FINDING-*]` lines, detect cascading failures:
- Same worker, sequential steps where earlier failed → later = cascade
- Missing artifacts after build/install failure → cascade
- Cross-worker: build fail + runtime dist/ missing + docs build failure → single root cause

**Only report root causes.** Cascade findings are sub-items.

## Step 5: Differential Analysis

File: `$PROJECT_ROOT/known-issues.jsonl`
Line format: `{"fp":"build-install-punycode","worker":"build","status":"FAIL","sev":"P3","desc":"...","first":"YYYYMMDD_HHmm","last":"...","count":N}`

| State | Action |
|-------|--------|
| New fingerprint | Add `count:1`, report |
| Known FAIL | Update `last`, increment `count`, skip |
| Known RESOLVED | Reopen as FAIL (regression), report |
| Was FAIL, not found | Set RESOLVED + `resolved_cycle` |
| RESOLVED 5+ cycles | Remove (cleanup) |

Fingerprint: `{worker}-{keyword}-{keyword}` (e.g. `runtime-redis-down`)

## Step 6: Severity Escalation

P1 count>=5→P0 | P2 count>=10→P1 | P3 count>=20→P2. Mark with `"escalated":true,"escalated_from":"{orig}"`.

## Step 7: Update & Report

1. Write updated `known-issues.jsonl` (fields: `fp`, `worker`, `status`, `sev`, `desc`, `first`, `last`, `count`, optional: `resolved_cycle`, `root_fp`, `escalated`, `escalated_from`)
2. Print summary:
```
=== QC Summary (Cycle {CYCLE_ID}) ===
Active: {n} | New: {n} | Resolved: {n} | Escalated: {n}
Stuck (count>=5): {fps}
Trend: IMPROVING / STABLE / DEGRADING
===
```
3. List new/regression/escalated findings with severity, worker, description

## Rules

- Do NOT fix issues — only report
- Do NOT modify project files other than `known-issues.jsonl`
- Max 10 new findings per cycle
- All workers failed → single finding: "QC workers failed"
- Supervisor is final authority on severity

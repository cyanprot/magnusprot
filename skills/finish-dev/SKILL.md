---
name: finish-dev
description: Use when implementation is complete and all tests pass — delegates to worktree-flow Phase 2 for branch completion
---

# Finishing a Development Branch

## Overview

This skill delegates to **worktree-flow Phase 2** for branch completion.

**Announce at start:** "I'm using the finish-dev skill to complete this work."

## Process

1. Verify tests pass (run project's test suite)
2. If tests fail: report failures, stop
3. If tests pass: invoke **worktree-flow Phase 2** (Completion)

## Why This Exists

`finish-dev` is a convenience entry point. The actual completion logic (merge/PR/cleanup options) lives in `worktree-flow` Phase 2 to avoid duplication.

## Integration

**Called by:**
- **execute-flow** (Step 5) — After all tasks complete
- Can be invoked standalone after any feature work

**Delegates to:**
- **worktree-flow** Phase 2 — Full branch completion lifecycle

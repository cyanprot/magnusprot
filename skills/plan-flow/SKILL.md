---
name: plan-flow
description: Use when planning multi-step tasks — generates TDD plan + campaign state
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the plan-flow skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorm-flow skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Library Lookup (Before Committing to Stack)

When the plan introduces a new library, framework, or SDK — **dispatch context7 BEFORE writing the plan**:

1. `mcp__plugin_context7_context7__resolve-library-id` with the library name
2. `mcp__plugin_context7_context7__query-docs` with the specific API/pattern the plan depends on
3. Paste the authoritative snippet into the plan as a code block (not paraphrased)

Skip this when the codebase already uses the library — the existing code is the source of truth.

Why: plans that cite training-cutoff API shapes break on execution. context7 catches version drift at plan time, not at task-3 failure time.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use execute-flow to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits

## Campaign & Sprint Contract Generation

After the plan is saved, generate campaign state and sprint contracts for multi-session persistence.

### Campaign File

Create `.claude/campaign.json` in the project root (create `.claude/` dir if needed):

```json
{
  "project": "<project-name>",
  "started": "<ISO 8601 now>",
  "current_phase": "plan",
  "current_sprint": 1,
  "total_sprints": <number of task groups>,
  "completed_sprints": [],
  "decisions_log": [],
  "continuation_prompt": "Plan created for <feature>. <total_sprints> sprints. Ready to execute with execute-flow."
}
```

### Sprint Contracts

For each logical sprint (task group), create `.claude/sprint-contracts/<project>-<N>.json`:

```json
{
  "sprint_id": "<project>-<N>",
  "feature": "<what this sprint builds>",
  "deliverables": ["<concrete outputs>"],
  "success_criteria": ["<measurable criteria — all must be met>"],
  "testable_behaviors": ["<user-observable behaviors to verify>"],
  "scope_boundaries": ["<what is NOT in scope>"],
  "evaluation_method": "unit"
}
```

**Rules:**
- One contract per sprint (group of related tasks)
- `success_criteria` must be objectively verifiable (not "works well")
- `scope_boundaries` prevent scope creep during execution
- Sprint grouping: related tasks that form a coherent deliverable (typically 2-5 tasks per sprint)

**Schema references:** `~/.claude/schemas/campaign.schema.json`, `~/.claude/schemas/sprint-contract.schema.json`

---

## Execution Handoff

After saving the plan and generating campaign/contracts, offer execution choice:

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** - Open new session with execute-flow, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use execute-flow (subagent mode)
- Stay in this session
- Fresh subagent per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses execute-flow (batch mode)

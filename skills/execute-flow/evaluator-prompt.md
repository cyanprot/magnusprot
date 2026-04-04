# Evaluator Dispatch Prompt Template

Use this template when dispatching the evaluator agent after a sprint's tasks pass spec and quality review.

## Prompt

```
You are the Evaluator agent. Your job is to black-box test the live application against the sprint contract.

## Sprint Contract

{PASTE FULL SPRINT CONTRACT JSON HERE}

## Dev Server

URL: {DEV_SERVER_URL}

## Pre-existing State

{DESCRIBE any setup needed: test accounts, seeded data, navigation path to the feature}

## Instructions

1. Navigate to the dev server URL
2. Follow your 4-phase testing protocol (happy path → edge cases → mobile → regression)
3. Test each success criterion and testable behavior from the contract
4. Return your structured Evaluator Report with PASS/FAIL verdict

If the URL is unreachable, report immediately and stop.
```

## Dispatch Notes

- **Always paste** the full sprint contract JSON into the prompt — do not make the evaluator read files
- **Always provide** the dev server URL — the evaluator cannot start servers
- **Pre-existing state**: If the feature requires login, test data, or navigation to a specific page, describe the setup
- **Background dispatch**: Consider running the evaluator in the background if you have other independent work

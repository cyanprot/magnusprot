---
name: llmcheck-dev
description: Use when checking LLM model settings across project
user-invocable: true
---

# /model-check - LLM Model Settings Verification

## Usage
- `/model-check` - Scan entire project
- `/model-check anthropic` - Anthropic models only
- `/model-check openai` - OpenAI models only

## Arguments: $ARGUMENTS
- Optional provider filter

## Target Patterns

### Anthropic Models
- `claude-3-opus`, `claude-3-sonnet`, `claude-3-haiku`
- `claude-3.5-sonnet`, `claude-3.5-haiku`
- `claude-opus-4`, `claude-sonnet-4`, `claude-haiku-4`
- `claude-opus-4-6`, `claude-sonnet-4-5`, `claude-haiku-4-5`

### OpenAI Models
- `gpt-5.2`, `gpt-5.1`, `gpt-5`, `gpt-5-mini`
- `gpt-4.1`, `gpt-4.1-mini`, `gpt-4.1-nano`
- `gpt-4o`, `gpt-4o-mini`
- `o3`, `o3-mini`, `o4-mini`
- `o4-mini-deep-research`

### Google Models
- `gemini-2.0-flash`
- `gemini-2.5-pro`, `gemini-2.5-flash`
- `gemini-3-pro-preview`, `gemini-3-flash-preview`
- `deep-research-pro-preview`

## Behavior

### Step 1: Search Config Files
```
Glob: ./**/*.{py,ts,js,json,yaml,yml,env,toml}
```

### Step 2: Search Model Name Patterns
```
Grep: (claude|gpt-[45]|gemini|o[34]-|deep-research)
```

### Step 3: Analyze Results
- List models per file
- Distinguish hardcoded model names vs environment variable references
- Warn on version mismatches (e.g., different versions in different files)

## Output Format
```
## LLM Model Settings

### Found Models
| File | Line | Model | Type |
|------|------|-------|------|
| config.py | 15 | claude-3.5-sonnet | Hardcoded |
| .env | 3 | gpt-4o | Env var |

### Warnings
- core/llm.py and config.py use different model versions

### Recommendations
- Manage model names via environment variables
- Centralize model version management in a single config file
```

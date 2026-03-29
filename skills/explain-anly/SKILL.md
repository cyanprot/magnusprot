---
name: explain-anly
description: Use when you need to understand code structure, functions, or files
user-invocable: true
argument-hint: "<file-or-symbol>"
---

# /explain - Code Explanation

Analyzes and explains the behavior of a specified file, function, or class.

## Usage
- `/explain src/auth.py` - Explain entire file
- `/explain UserService` - Find and explain class/function
- `/explain src/api/routes/` - Explain directory structure
- `/explain src/main.py:handle_request` - Explain specific function

## Arguments: $ARGUMENTS
- File path: Analyze that file
- Symbol name: Search project-wide then analyze
- Directory: Explain structure and roles
- `file:function_name`: Analyze specific function only

## Behavior

### File Path
1. Read entire file
2. Identify structure (imports, classes, functions)
3. Explain each component's role
4. Analyze external dependencies

### Symbol Name
1. Search for the definition in the project
2. Read related code
3. Identify call relationships (caller/callee)
4. Explain behavior

### Directory
1. List files
2. Summarize each file's role
3. Explain inter-module relationships

## Output Format
```
## Code Explanation: <target>

### Overview
This file/function/class handles ...

### Structure
- `class UserService` - User-related business logic
  - `authenticate()` - Authentication handling
  - `create_user()` - User creation

### Core Logic
1. Receive request -> 2. Validate -> 3. Query DB -> 4. Return response

### Dependencies
- `database.py` -> DB session
- `models.py` -> User model
- `schemas.py` -> I/O schemas

### Call Graph
- Called by: `api/routes/auth.py:login()`
- Calls: `database.get_session()`, `User.verify_password()`
```

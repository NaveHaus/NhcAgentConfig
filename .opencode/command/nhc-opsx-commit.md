---
name: nhc-opsx-commit
description: Commit staged changes
---

Commit staged changes that optionally correspond to an archived OpenSpec change.

**Input**: Optionally specify a change name (e.g., `/nhc-opsx-commit add-auth`).

## Requirements (MANDATORY)

- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.
- DO NOT assume or attempt to deduce a change name if it appears one is not provided as input.
- DO NOT execute any `git` commands other than those specified by the `nhc-openspec-commit` skill.

## Steps
- Invoke the `nhc-openspec-commit` skill to complete the commit.
---
name: nhc-openspec-commit
description: Commit current or archived OpenSpec artifact changes.
license: MIT
metadata:
  author: NaveHaus Consulting LLC
---

**Input**: Optionally specify a current or archived OpenSpec change name.

## Requirements (MANDATORY)

- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.
- DO NOT assume or attempt to deduce an OpenSpec change name if it appears one is not provided as input.
- DO NOT execute any `git` commands other than those specified by this skill.

## Steps

1. **If an OpenSpec change name is NOT explicitly provided by the user**: create a list of directories containing uncommited OpenSpec changes using the following commands:
  - Generate list of untracked OpenSpec changes:
    ```
    git ls-files --others --exclude-standard | rg -o '^openspec/changes/[^/]+' | rg -v '^openspec/changes/archive/.*' | sort -u
    ```
  - Generate list of unstaged OpenSpec changes:
    ```
    git diff --name-only | rg -o '^openspec/changes/[^/]+' | rg -v '^openspec/changes/archive/.*' | sort -u
    ```
  - Generate list of staged OpenSpec changes:
    ```
    git diff --staged --name-only | rg -o '^openspec/changes/[^/]+' | rg -v '^openspec/changes/archive/.*' | sort -u
    ```
  - Generate list of unstaged archived OpenSpec changes:
    ```
    git diff --name-only | rg -o '^openspec/changes/archive/[0-9]{4}-[0-9]{2}-[0-9]{2}-[^/]+' | sort -u
    ```
  - Generate list of staged archived OpenSpec changes:
    ```
    git diff --staged --name-only | rg -o '^openspec/changes/archive/[0-9]{4}-[0-9]{2}-[0-9]{2}-[^/]+' | sort -u
    ```
2. **Ask the user** how to proceed with commit. Evaluate each of the following **IF** statements, present the user with a **CORRECTLY NUMBERED** list of the choices following the correct **IF** statements, then **ask the user** to select ONE choice by number:
  1. **IF the directory list contains EXACTLY one matched change path**: Commit `<change-name>`.
    > **IMPORTANT** Deduce the `<change-name>` to display from the matched change path.
  1. **IF the directory list contains two or more matched change paths**: Select one or more changes to include in the commit.
    > **IMPORTANT** If the user selects this option, you MUST present the user with a **NUMBERED** list of `<change-name>` items to select from, including an "all" option. Deduce the `<change-name>` to display for each item from the corresponding matched change path.
  1. Cancel the commit.
    > **IMPORTANT** Always include this option.
3. Execute the requested action:
  - **IF the user chooses to cancel the commit**: STOP.
  - **IF the user selects EXACTLY one change to commit**:
    - Announce "Generating commit for change: <change-name>".
    - Generate the **Content to summarize** from the path to `proposal.md` under the matched change path the user selected.
  - **IF the user selects two or more changes to commit**:
    - Announce "Generating commit for changes: <change-name>, <change-name>, ...".
    - Generate the **Content to summarize** as the list of paths to `proposal.md` files under all of the matched change paths the user selected.
4. **Complete the commit**:
  - Invoke the `nhc-conventional-commit` skill to complete the commit with the generated **Content to summarize**.
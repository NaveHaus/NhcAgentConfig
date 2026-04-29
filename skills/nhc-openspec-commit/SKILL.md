---
name: nhc-openspec-commit
description: Commit current or archived OpenSpec artifact changes.
license: MIT
metadata:
  author: NaveHaus Consulting LLC
---

**Input**: Optionally specify the name of an OpenSpec change or change archive

## Requirements (MANDATORY)

- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.
- DO NOT assume or attempt to deduce an OpenSpec change name if it appears one is not provided as input.
- DO NOT execute any `git` commands other than those specified by this skill.

## Steps
1. **If an OpenSpec change or change archive name is NOT explicitly provided by the user**: create a list of commit work items for directories containing uncommitted OpenSpec changes and change archives:
   - Generate the list of changes:
     ```bash
     git status --porcelain=v1 -z -- openspec/changes
     ```
   - Parse the NUL-delimited output and inspect each changed path.
   - For each path matching an active change directory, `openspec/changes/<change-name>/...`, create or update a work item keyed by `<change-name>` with these fields:
     - `change-name`: `<change-name>`
     - `change-path`: `openspec/changes/<change-name>`
   - For each path matching a dated change archive directory, `openspec/changes/archive/<YYYY-MM-DD>-<change-name>/...`, create or update a work item keyed by `<change-name>` with these fields:
     - `change-name`: `<change-name>`
     - `archive-path`: `openspec/changes/archive/<YYYY-MM-DD>-<change-name>`
   - After parsing all paths, classify each work item and generate a list of work items:
       > **IMPORTANT** The result MUST be the list of work items, not a flat list of paths.
     - **IF** the work item has `change-path` and does NOT have `archive-path`: It is an independent active change commit.
     - **IF** the work item has both `change-path` and `archive-path`: It is a paired archived change commit. The active change and dated archive addition MUST be committed together.
     - **IF** the work item has `archive-path` and does NOT have `change-path`: It is an independent archive commit.
2. **Ask the user** how to proceed with commit. Evaluate each of the following **IF** statements, present the user with a **CORRECTLY NUMBERED** list of the choices following the true **IF** statements, then **ask the user** to select ONE choice by number:
   - **IF** the work item list contains EXACTLY one item: Commit `<change-name>`.
      > **IMPORTANT** Deduce `<change-name>` from the work item.
   - **IF** the work item list contains two or more items: Select one or more changes to include in the commit.
      > **IMPORTANT** If the user selects this option, you MUST present the user with a **NUMBERED** list of `<change-name>` items to select from, including an "all" option. Deduce the `<change-name>` for each item from the corresponding work item.
   - Cancel the commit.
      > **IMPORTANT** Always include this option.
3. Execute the requested action:
   - **IF** the user chooses to cancel the commit: STOP.
   - **IF** the user selects EXACTLY one work item to commit:
     - Announce "Generating commit for: \<change-name\>".
     - Generate the **Content to summarize** from the path to `proposal.md` under the matched change path the user selected.
   - **IF** the user selects two or more work items to commit:
     - Announce "Generating commit for changes: \<change-name\>, \<change-name\>, ...".
     - Generate the **Content to summarize** as the list of paths to `proposal.md` files under all of the matched change paths the user selected.
4. **Complete the commit**:
   - Invoke the `nhc-conventional-commit` skill to complete the commit with the generated **Content to summarize**.
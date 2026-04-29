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
1. **IF** an OpenSpec change or change archive name is NOT explicitly provided by the user: create a list of commit work items for directories containing uncommitted OpenSpec changes, change archives, and matching synced OpenSpec specs:
    - Compute the list of work item source paths:
      - Generate the list of uncommitted OpenSpec changes, change archives, and synced specs:
        ```bash
        git status --porcelain=v1 -z --untracked-files=all -- openspec/changes openspec/specs
        ```
      - Parse the NUL-delimited output and inspect each changed path.
    - Compute the list of work items:
      - For each path matching an active change directory, `openspec/changes/<change-name>/...`, create or update a work item keyed by `<change-name>` with:
        - `change-name`: `<change-name>`
        - `change-path`: `openspec/changes/<change-name>`
      - For each path matching a dated change archive directory, `openspec/changes/archive/<YYYY-MM-DD>-<change-name>/...`, create or update a work item keyed by `<change-name>` with:
        - `change-name`: `<change-name>`
        - `archive-path`: `openspec/changes/archive/<YYYY-MM-DD>-<change-name>`
      - For each path matching a spec inside an active change or dated change archive, `openspec/changes/<change-name>/specs/<spec-name>/...` or `openspec/changes/archive/<YYYY-MM-DD>-<change-name>/specs/<spec-name>/...`, record `<spec-name>` in that work item’s `spec-names` list.
      - For each path matching a synced OpenSpec spec directory, `openspec/specs/<spec-name>/...`, collect `openspec/specs/<spec-name>` as a pending synced spec path.
    - Attach synced spec paths to work items:
      - **IF** exactly one work item matches the synced spec name: Add `openspec/specs/<spec-name>` to that work item’s `spec-paths` list.
      - **IF** no work item matches the synced spec name: Keep the synced spec path unmatched and do not silently attach it to any work item.
      - **IF** two or more work items match the synced spec name: **Ask the user** how to resolve the ambiguity before committing; DO NOT guess.
    - Classify each work item:
      - **IF** the work item has `change-path` and does NOT have `archive-path`: It is an independent active change commit.
      - **IF** the work item has both `change-path` and `archive-path`: It is a paired archived change commit. The active change, dated archive addition, and matching synced specs under `openspec/specs` MUST be committed together.
      - **IF** the work item has `archive-path` and does NOT have `change-path`: It is an independent archive commit. The dated archive addition and matching synced specs under `openspec/specs` MUST be committed together.
    - Pass the generated work item list to step 2.
      > **IMPORTANT** The result MUST be the list of work items, not a flat list of paths.
  2. **Ask the user** how to proceed with commit. Evaluate each of the following **IF** statements, present the user with a **CORRECTLY NUMBERED** list of the choices following the true **IF** statements, then **ask the user** to select ONE choice by number:
     - **IF** discovery found one or more unmatched synced spec paths: Report the unmatched synced spec paths and ask whether to continue without them or cancel the commit.
        > **IMPORTANT** Unmatched synced spec paths MUST NOT be silently included in any commit.
     - **IF** discovery found an ambiguous synced spec path that matches two or more work items: Report the ambiguous synced spec path and matching `<change-name>` values, then ask the user to select the correct work item or cancel the commit.
        > **IMPORTANT** Ambiguous synced spec paths MUST be resolved before presenting commit choices.
     - **IF** the work item list contains EXACTLY one item and no unmatched or ambiguous synced spec paths remain: Commit `<change-name>`.
        > **IMPORTANT** Deduce `<change-name>` from the work item. The commit MUST include the work item’s `change-path`, `archive-path`, and `spec-paths` values when present.
     - **IF** the work item list contains two or more items and no unmatched or ambiguous synced spec paths remain: Select one or more changes to include in the commit.
        > **IMPORTANT** If the user selects this option, you MUST present the user with a **NUMBERED** list of `<change-name>` items to select from, including an "all" option. Deduce `<change-name>` from each corresponding work item. Each selected commit MUST include that work item’s `change-path`, `archive-path`, and `spec-paths` values when present.
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
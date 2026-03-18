---
name: nhc-openspec-commit
description: Commit staged OpenSpec changes. Use when the user wants to commit all git changes due to completing an OpenSpec workflow.
license: MIT
compatibility:
  - Requires openspec CLI.
  - Requires ripgrep (`rg`).
metadata:
  author: NaveHaus Consulting LLC
---

Commit staged changes that optionally correspond to an archived OpenSpec change.

**Input**: Optionally specify an OpenSpec change name.

## Requirements (MANDATORY)

- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.
- DO NOT assume or attempt to deduce a change name if it appears one is not provided as input.
- DO NOT execute any `git` commands other than those specified by this skill.

## Steps

1. **If a change name is NOT explicitly provided by the user**
  - YOU MUST parse the output of `git diff --staged --name-only` to discover unique directories having the format `openspec/changes/archive/YYYY-MM-DD-<change-name>` that contain staged archived OpenSpec changes, e.g. using ripgrep (`rg`):
    ```bash
    git diff --staged --name-only | rg -o '^openspec/changes/archive/[0-9]{4}-[0-9]{2}-[0-9]{2}-[^/]+' | sort -u
    ```
  - YOU MUST ask the user to choose one of these options:
    - IF one or more directories were found, select a directory from the list of discovered directories.
    - Proceed with the commit using the currently staged changes without referencing a specific change.
    - Cancel the commit.
  - IF the user chooses to cancel, STOP.
  - IF the user chooses to proceed WITHOUT referencing a specific change, proceed directly to Step 3, "Complete the commit".
  - IF the user selects a directory, continue to Step 2, "If a change name is provided" providing the `<change-name>` part of the selected directory as the change name.

2. **If a change name is provided**
  - Announce "Generating commit for change: <change-name>".
  - Verify that `openspec/changes/archive/YYYY-MM-DD-<change-name>/proposal.md` exists and is accessible.
    - If the expected path to `proposal.md` is invalid, ask the user to provide the path to the right `proposal.md` file.
    - If the user provides the path to `proposal.md`:
      - Proveed with Step 3, "Complete the commit".
      - **Content to summarize**: **Important** Include all lines of `proposal.md` as the content to summarize for the conventional-commits message.
    - Otherwise, STOP.

3. **Complete the commit**
  - Invoke the conventional-commits skill to complete the commit.
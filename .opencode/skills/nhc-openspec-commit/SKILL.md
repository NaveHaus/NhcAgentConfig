---
name: nhc-openspec-commit
description: Commit staged OpenSpec changes. Use when the user wants to commit all git changes due to completing an OpenSpec workflow.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: NaveHaus Consulting LLC
---

Commit staged changes that optionally correspond to an Openspec change.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **If a change name is NOT provided**
  - Use any suitable **Ask**-like tool to give the user the option to select an existing openspec change or to just continue the commit with the currently staged changes:
    - If the user opts to select an existing change:
      - Run `openspec list --json` to get available changes.
      - **Important**: Show only active changes (not already archived).
      - Use any suitable "**Ask**"-like tool if available to let the user select.
      - If the user selects a change, continue with the "If a change name is provided:" instructions using the selected change name.
    - Otherwise: continue with "3. Generate the commit message:" instructions.

2. **If a change name is provided**
  - Announce "Generating commit for change: <name>".
  - Parse the JSON output of `openspec status --change "<name>" --json`:
    - If a JSON object was returned, make note of:
      - `schemaName`: The workflow being used (e.g., "spec-driven")
      - Which artifact contains the tasks (typically "tasks" for spec-driven, check status for others)
    - Otherwise:
      - Present the exact error message to the user.
      - STOP
  - Parse the output of `openspec instructions apply --change "<name>" --json`:
    - Handle the state:
      - If `state: "blocked"` (missing artifacts):
        - Show message, suggest using `/opsx-continue`.
        - STOP
    - Determine the path to the artifact containing tasks:
      - If the path to the tasks artifact can not be reliably determined from the output, ask the user to provide the path.
      - If the user does not provide the path:
        - STOP
    - Continue with "3. Generate the commit message:" instructions.
      - **Content to summarize**: **Important** ONLY summarize the contents of the design artifact to summarize the conventional-commits message.

3. **Generate the commit message**
   - Invoke the conventional-commits skill to complete the commit.
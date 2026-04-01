---
name: nhc-opsx-verify
description: Verify implementation matches OpenSpec change artifacts.
---

Verify that an implementation matches the change artifacts (specs, tasks, design).

**Input**: Optionally specify a change name (e.g., `/nhc-opsx-verify add-auth`).

# Initialization

**If no change name provided, prompt for selection**

   Run `openspec list --json` to get available changes. Use the **AskUserQuestion tool** to let the user select.

   Show only active changes (not already archived).
   Include the schema used for each change if available.

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

# Requirements (MANDATORY)
- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.

# Verification Status Record (MANDATORY)
- Create the file `openspec/changes/<change-name>/verification-status.md` to track the current status of findings surfaced by openspec-verify.
- This file MUST be used to track the current status of findings and working decisions for the `<change-name>` change.
- See [Example Status File](#example-scratchpad-file) for the required format.

# Status File Rules (MANDATORY)
- The finding status MUST reflect the state of the openspec artifacts and source code involved (see [Status Legend](#status-legend)).
- The "Last updated" date MUST be kept current.
- You MUST list groups of findings in order of severity, i.e. `CRITICAL`, `WARNING`, and `SUGGESTION`, using a numbered list to annotate the findings in the group.
- You MUST order the findings in each group:
  - By dependency resolution order: e.g. if `CRITICAL` problems `Feature B is missing` and `Feature A does not match the spec` are found, and `Feature B` depends upon `Feature A` in the spec, then `Feature A does not match the spec` MUST come before `Feature B is missing` due to the dependency.
  - By decreasing important relative to other findings in the same category, if relevant and possible to determine.

# Steps (MANDATORY)
1. Invoke the `openspec-verify` skill to initiate the implementation verification review round if a round has not already started (`openspec/changes/<change-name>/verification-status.md` does not exist).
2. If the verification status file does not exist, create one for the current round with the initial findings (see [Verification Status Record](#verification-status-record-mandatory))
3. **Ask the user** how to proceed with remediation, e.g. by presenting options to:
  - Remediate all findings in one shot; or
  - Remediate findings in a single severity group in one shot, with user confirmation before continuing to the next severity group; or
  - Remediate findings one-at-a-time, with user confirmation to continue to the next finding after each one; or
  - Select one or more findings to remediate across all severity groups, with user confirmation after each one.
4. **IMPORTANT** Verify that relevant openspec artifacts are consistent with the change(s) made to remediate the selected finding(s).
5. Ensure the status of each finding is updated as it is addressed.
6. Once the findings have been remediated for the current `openspec-verify` round, offer:
  - To stage and commit the current changes (if a `conventional-commit` skill is available, offer to use it); or
  - To start a new `openspec-verify` round to check for missed problems.

# Example Status File
```markdown
## <change-name> Verification Status

This file tracks the current openspec-verify findings list and working decisions
for the implementation of the `<change-name>` change. Treat this as the running memory for the
implementation verification process; it is NOT a spec artifact.

Last updated: YYYY-MM-DD

## Status Legend
- Unresolved: The implementation does not match the change artifacts.
- In progress: The implementation is being updated to remediate the finding.
 Deferred: The finding will be fixed in a subsequent change:
  - The user MAY explicitly request this this status for ALL severity levels.
  - You MAY suggest this status for ALL severity levels, but the suggestion MUST be justified and it MUST be confirmed by the user before applying it.
  - The user MUST be advised with a concrete reason NOT to select this status for `CRITICAL` findings.
- Completed: The implementation has been made **Complete**, **Correct**, and **Coherent**

## Key References

- LIST ANY RELEVANT REFERENCES USED TO RESOLVE FINDINGS

## Current Working Constraints / Decisions

- LIST ANY CONSTRAINTS OR DECISIONS AFFECTING REMEDIATION OF THE FINDING CURRENTLY BEING ADDRESSED

## Findings List

### CRITICAL
1. Description of the first `CRITICAL` finding, in dependency-then-decreasing-importance order
  - Status: <status>
  - Notes:
    - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS DECISION
  - Artifacts touched:
    - `openspec/changes/<change-name>/...`
    - ...
  - Sources touched, using relative paths:
    - <relative path>/...

2. ...

### WARNING

1. Description of the first `WARNING` finding, in dependency-then-decreasing-importance order
  - Status: <status>
  - Notes:
    - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS DECISION
  - Artifacts touched:
    - `openspec/changes/<change-name>/...`
    - ...
  - Sources touched, using relative paths:
    - <relative path>/...

2. ...

### SUGGESTION

1. Description of the first `SUGGESTION` finding, in dependency-then-decreasing-importance order
  - Status: <status>
  - Notes:
    - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS DECISION
  - Artifacts touched:
    - `openspec/changes/<change-name>/...`
    - ...
  - Sources touched, using relative paths:
    - <relative path>/...

2. ...

## Open Questions (Need Clarification)
- LIST ANY OPEN QUESTIONS FOR THE FINDING CURRENTLY BEING ADDRESSED
```
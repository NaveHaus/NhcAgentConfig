---
name: nhc-openspec-verify
license: 'MIT'
compatibility: 'Requires openspec CLI.'
description: Verify implementation matches OpenSpec change artifacts.
---

Verify that an implementation matches the change artifacts (specs, tasks, design).

**Input**: Optionally specify a change name (e.g., `/nhc-openspec-verify add-auth`).

# Initialization

**If no change name provided, prompt for selection**

   Run `openspec list --json` to get available changes. Present the user with a numbered list of changes, then **ask the user** to select a change by number.

   Show only active changes (not already archived).
   Include the schema used for each change if available.

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

# Requirements (MANDATORY)
- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.

# Verification Status Record (MANDATORY)
- Create the file `openspec/changes/\<change-name\>/verification-status.md` to track the current status of findings surfaced by the `openspec-verify` skill.
- This file MUST be used to track the current status of findings and working decisions for the `\<change-name\>` change.
- See [Example Verification Status File](#example-verification-status-file) for the required format.

# Remediation Status Labels
- Unresolved: The implementation does not match the change artifacts.
- In progress: The implementation is being updated to remediate the finding.
- Deferred: The finding will be fixed in a subsequent change:
  - The user MAY explicitly request this this status for ALL severity levels.
  - You MAY suggest this status for ALL severity levels, but the suggestion MUST be justified and it MUST be confirmed by the user before applying it.
  - The user MUST be advised with a concrete reason NOT to select this status for `CRITICAL` findings.
- Completed: The implementation has been made **Complete**, **Correct**, and **Coherent**

# Status File Rules (MANDATORY)
- The finding status MUST reflect the state of the openspec artifacts and source code involved (see [Remediation Status Labels](#remediation-status-labels)).
- The "Last updated" date MUST be kept current.
- You MUST list groups of findings in order of severity, i.e. `CRITICAL`, `WARNING`, and `SUGGESTION`, using a numbered list to annotate the findings in the group.
- You MUST order the findings in each group:
  - By dependency resolution order: e.g. if `CRITICAL` problems `Feature B is missing` and `Feature A does not match the spec` are found, and `Feature B` depends upon `Feature A` in the spec, then `Feature A does not match the spec` MUST come before `Feature B is missing` due to the dependency.
  - By decreasing important relative to other findings in the same category, if relevant and possible to determine.

# Steps (MANDATORY)
**CRITICAL**:
- Before performing any action, you MUST state the current step number you are executing (e.g., `Executing Step X:`).
- You MUST wait for the user to respond before advancing from any step that requires user input.
- You MUST implement the remediation(s) ONLY for the finding(s) selected by the user.
- You MUST update the status of each finding using a defined Status Label as it is being addressed (see [Remediation Status Labels](#remediation-status-labels)).
- You MUST ensure that ALL relevant openspec artifacts are consistent with your changes.

1. Determine if an verification round is in progress. Evaluate the two following **IF** statements, then execute the command following the ONE true **IF** statement:
  - **IF** `openspec/changes/\<change-name\>/verification-status.md` exists: present the following options to the user, then **ask the user** to select ONE option to proceed with:
    1. Execute a verification using the `openspec-verify` skill and **UPDATE** the existing file with new findings.
    2. Execute a verification using the `openspec-verify` skill and **REPLACE** the existing file with new findings.
    3. Stop the round.
  - **IF** `openspec/changes/\<change-name\>/verification-status.md` does NOT exist:
    - Invoke the `openspec-verify` skill to initiate a new round; THEN
    - Create `openspec/changes/\<change-name\>/verification-status.md` with the initial findings (see [Example Verification Status File](#example-verification-status-file)).
2. **Ask the user** how to proceed. Evaluate each of the following **IF** statements, present the user with a **CORRECTLY NUMBERED** list of the choices following the true **IF** statements, then **ask the user** to select ONE choice by number:
  1. **IF** there is **EXACTLY** 1 finding: Remediate \<finding-summary\>.
    > **IMPORTANT** \<finding-summary\>
  1. **IF** there are 2 or more findings: Select findings to remediate one-at-a-time, with user confirmation to continue after remediating each finding.
    > **IMPORTANT** If the user selects this option, you MUST present the user with a **CORRECTLY NUMBERED** list of findings ordered by decreasing severity, including an "all" option.
  1. **IF** there are 2 or more findings: Remediate all findings in one shot.
  1. Stop the verification round.
    > **IMPORTANT** Always include this option.
3. **Ask the user** how to proceed. Evaluate each of the following **IF** statements, present the user with a **CORRECTLY NUMBERED** list of the choices following the true **IF** statements, then **ask the user** to select ONE choice by number:
  1. **IF** all findings are `Completed` in `verification-status.md`: Start a new round to check for additional or overlooked findings.
    > **IMPORTANT** If the user selects this option, return to Step 1.
  1. **IF** there are `Unresolved` findings remaining in `verification-status.md`: Continue remediation.
    > **IMPORTANT** If the user selects this option, return to Step 2.
  1. **IF** there are uncommited verification changes: Stage and `nhc-conventional-commit` the current remediation changes.
    > **IMPORTANT** If the user selects this option, return to Step 3.
  1. Stop the verification round.
    > **IMPORTANT** Always include this option.

# Example Verification Status File
```markdown
## \<change-name\> Verification Status

This file tracks the current `openspec-verify` findings list and working decisions
for the implementation of the `\<change-name\>` change. Treat this as the running memory for the
implementation verification process; it is NOT a spec artifact.

Last updated: YYYY-MM-DD

### Summary
| Dimension    | Status           |
|--------------|------------------|
| Completeness | X/Y tasks, N reqs|
| Correctness  | M/N reqs covered |
| Coherence    | Followed/Issues  |

## Key References

- LIST ANY RELEVANT REFERENCES USED TO RESOLVE FINDINGS

## Current Working Constraints / Decisions

- LIST ANY CONSTRAINTS OR DECISIONS AFFECTING REMEDIATION OF THE FINDING CURRENTLY BEING ADDRESSED

## Findings List by Priority

### CRITICAL
1. Description of the first `CRITICAL` finding, in dependency-then-decreasing-importance order
  - Status: \<status-label\>
  - Notes:
    - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS DECISION
  - Artifacts touched:
    - `openspec/changes/\<change-name\>/...`
    - ...
  - Sources touched, using relative paths:
    - \<relative path\>/...

2. ...

### WARNING

1. Description of the first `WARNING` finding, in dependency-then-decreasing-importance order
  - Status: \<status-label\>
  - Notes:
    - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS DECISION
  - Artifacts touched:
    - `openspec/changes/\<change-name\>/...`
    - ...
  - Sources touched, using relative paths:
    - \<relative path\>/...

2. ...

### SUGGESTION

1. Description of the first `SUGGESTION` finding, in dependency-then-decreasing-importance order
  - Status: \<status-label\>
  - Notes:
    - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS DECISION
  - Artifacts touched:
    - `openspec/changes/\<change-name\>/...`
    - ...
  - Sources touched, using relative paths:
    - \<relative path\>/...

2. ...

## Open Questions (Need Clarification)
- LIST ANY OPEN QUESTIONS FOR THE FINDING CURRENTLY BEING ADDRESSED
```

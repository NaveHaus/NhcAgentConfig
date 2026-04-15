---
name: nhc-opsx-refine
description: A quality review skill that refines OpenSpec changes to implementation-ready quality.
---

Perform cross-artifact consistency checks on spec.md/design.md/tasks.md, commonality analysis, and best-practice validation.

**Input**: Optionally specify a change name (e.g., `/nhc-opsx-refine add-auth`).

# Initialization

**If no change name provided, prompt for selection**

   Run `openspec list --json` to get available changes. Present the user with a numbered list of changes, then **ask the user** to select a change by number.

   Show only active changes (not already archived).
   Include the schema used for each change if available.

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

# Terminology
- Both "issues" and "gaps" refer to the same concept of a mismatch or discrepancy in one or more artifacts.

# Requirements (MANDATORY)
- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.

# Quality Review Scratchpad (MANDATORY)
- Create the scratchpad file `openspec/changes/<change-name>/scratchpad.md` to track the current status of issues found by `openspec-refine`.
- The scratchpad MUST be used to track the current issue list and working decisions for the `<change-name>` change.
- See [Example Scratchpad File](#example-scratchpad-file) for the required scratchpad format.

# Issue Status Labels
- Open: Not yet captured consistently in OpenSpec artifacts.
- Needs refinement: Partially captured; artifacts still need clarifications/consistency work.
- Consistent: Artifacts are aligned with current intended behavior (may still evolve as scope grows).
- Accepted: User accepted the affected artifact(s) as-is.
  - The user MAY explicitly request this this status for ALL priorities.
  - You MAY suggest this status for ALL priorities, but the suggestion MUST be justified and it MUST be confirmed by the user before applying it.
  - The user MUST be advised with a concrete reason NOT to select this status for P2 issues.

# Scratchpad Rules (MANDATORY)
- The issue status MUST reflect the state of the openspec artifacts involved in the issue (see [Status Legend](#status-legend)).
- DO NOT record the implemention status, as this is tracked by openspec artifacts separately.
- The "Last updated" date MUST be kept current.
- You MUST present the user with best-practice search themes to review prior to creating or updating the scratchpad.
- You MUST list issues in order of priority, i.e. P0, P1, P2, with the format `P<L>(i)`, where `L` is the level and `(i)` is the index of the issue in the list of all issues at level `<L>`.

# Steps (MANDATORY)
- Invoke the `openspec-refine` skill to complete the openspec artifact quality review.
- You MUST propose an improvement strategy if issues exist.
- DO NOT proceed with addressing issues without user confirmation of the issues to fix.

# Example Scratchpad File
```markdown
## <change-name> Scratchpad

This file tracks the current openspec-refine issue list and working decisions
for the `<change-name>` change. Treat this as the running memory for the
refine/clarification process; it is NOT a spec artifact.

Last updated: YYYY-MM-DD

## Status Legend
- Open: Not yet captured consistently in OpenSpec artifacts.
- Needs refinement: Partially captured; artifacts still need clarifications/consistency work.
- Consistent: Artifacts are aligned with current intended behavior (may still evolve as scope grows).
- Accepted: User accepted the affected artifact(s) as-is.

## Key References

- LIST ANY RELEVANT REFERENCES USED TO RESOLVE ISSUES

## Current Working Constraints / Decisions

- LIST ANY CONSTRAINTS OR DECISIONS AFFECTING RESOLUTION OF THE ISSUE CURRENTLY BEING ADDRESSED

## Issue List

### P0(1): ...
- Status: <status>
- Notes:
  - LIST ANY NOTES TO CLARIFY THE CURRENT STATUS CHOICE
- Artifacts touched:
  - `openspec/changes/<change-name>/...`
  - ...

### P0(2): ...

### P1(1): ...

### P2(1): ...

...

## Open Questions (Need Clarification)
- LIST ANY OPEN QUESTIONS FOR THE ISSUE CURRENTLY BEING ADDRESSED
```
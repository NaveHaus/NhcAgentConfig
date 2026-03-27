---
name: nhc-opsx-refine
description: A quality review skill that refines OpenSpec changes to implementation-ready quality.
---

Perform cross-artifact consistency checks on spec.md/design.md/tasks.md, commonality analysis, and best-practice validation.

**Input**: Optionally specify a change name (e.g., `/nhc-opsx-refine add-auth`).

# Initialization

**If no change name provided, prompt for selection**

   Run `openspec list --json` to get available changes. Use the **AskUserQuestion tool** to let the user select.

   Show only active changes (not already archived).
   Include the schema used for each change if available.

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

# Requirements (MANDATORY)
- DO follow the steps EXACTLY.
- DO NOT add, remove, or modify steps.

# Quality Review Scratchpad (MANDATORY)
- Create the scratchpad file `openspec/changes/<change-name>/scratchpad.md` to track the current status of issues found by openspec-refine.
- The scratchpad MUST be used to track the current issue list and working decisions for the `<change-name>` change.
- See [Example Scratchpad File](#example-scratchpad-file) for the required scratchpad format.

# Scratchpad Rules (MANDATORY)
- The issue status MUST reflect the state of the openspec artifacts involved in the issue (see [Status Legend](#status-legend)).
- DO NOT record the implemention status, as this is tracked by openspec artifacts separately.
- The "Last updated" date MUST be kept current.
- You MUST include best-practice search themes review if the user does not confirm prior to creating the scratchpad.
- You MUST list issues in order of priority, i.e. P0, P1, P2, with the format `P<L>(i)`, where `L` is the level and `(i)` is the index of the issue in the list of all issues at level `<L>`.

# Steps (MANDATORY)
- Invoke the `openspec-refine` skill to complete the openspec artifact quality review.

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
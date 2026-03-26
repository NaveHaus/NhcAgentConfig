Create a scratchpad markdown file under `openspec/changes/<change-name>` to track the current status of issues found by openspec-refine.
The scratchpad MUST be used to track the current issue list and working decisions for the `<change-name>` change.

## Rules (MANDATORY)
- The issue status MUST reflect the state of the openspec artifacts involved in the issue (see [Status Legen](#status-legend)).
- DO NOT record the implemention status, as this is tracked by openspec artifacts separately.
- The "Last updated" date MUST be kept current.
- You MUST include best-practice search themes review if the user does not confirm prior to creating the scratchpad.
- You MUST list issues in order of priority, i.e. P0, P1, P2, with the format `P<L>(i)`, where `L` is the level and `(i)` is the index of the issue in the list of all issues at level `<L>`.

# EXAMPLE FILE

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
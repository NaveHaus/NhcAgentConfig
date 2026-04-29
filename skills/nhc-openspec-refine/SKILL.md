---
name: nhc-openspec-refine
license: 'MIT'
compatibility: 'Requires openspec CLI.'
description: Review and refine change artifacts for cross-artifact consistency, best-practice alignment, and implementation readiness. Tracks issues in a scratchpad file scoped to the change.
metadata:
  references:
    - https://gist.github.com/shohu/a219c8137f2967d720552a304badeeee
    - https://github.com/Fission-AI/OpenSpec/pull/893
---

Perform cross-artifact consistency checks on `spec.md`, `design.md`, and `tasks.md`, including commonality analysis and best-practice validation.

**Input**: Optional change name (e.g., `add-auth`)

**If no change name was provided, prompt the user to select a change**
- Run `openspec list --json` to get available changes, then present the user with a **NUMBERED** list of changes and **ask the user** to select a change to refine.
- Show only active changes (not already archived)

> **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

# Issue Priorities
- P0: Required, MUST be resolved to address a MAJOR implementation-readiness deficiency in artifact correctness, consistency, and/or quality.
- P1: Recommended, SHOULD be resolved to address a MINOR implementation-readiness deficiency in artifact correctness, consistency, and/or quality.
- P2: Optional, MAY be resolved to improve implementation readiness by enhancing artifact correctness, consistency, and/or quality.

# Steps
- You MUST follow ALL steps of this skill EXACTLY
- DO NOT add, remove, or modify steps

1. **Read change artifacts**
  ```bash
  openspec status --change "<name>" --json
  ```

  > **IMPORTANT** Read all existing artifacts (`proposal.md`, `design.md`, `tasks.md`, `specs/*.md`) from the change directory.

2. **Create or continue the scratchpad**

  Evaluate the following two **IF** statements, then execute the command following the ONE true **IF** statement:
  - **IF** `openspec/changes/<change-name>/scratchpad.md` does NOT exist: create `openspec/changes/<change-name>/scratchpad.md` using the format in [Scratchpad Format](#scratchpad-format)
  - **IF** `openspec/changes/<change-name>/scratchpad.md` exists: present the following options to the user as a **NUMBERED** list, then **ask the user** to select ONE option to proceed with:
    - Perform a new review and **UPDATE** the existing file with new issues
    - Perform a new review and **REPLACE** the existing file with new issues
    - Address unresolved issues in the scratchpad. (ONLY show this option **IF** `scratchpad.md` contains unresolved issues.)
      > **IMPORTANT**: If the user selects this option, continue with **Execute remediation strategy**.
    - Stop the refinement.

  > **IMPORTANT**: You MUST follow the rules in [Scratchpad Rules](#scratchpad-rules) to maintain the scratchpad.

3. **Artifact quality issues**

  Check artifacts against common quality standards:
  - P0: `tasks.md` is broken down into implementable granularity
  - P0: No circular, ambiguous, or missing task dependencies
  - P0: `spec.md` contains normative content only
  - P0: Scenario acceptance criteria are explicit and measurable (GIVEN/WHEN/THEN)
  - P0: Each requirement has at least one `#### Scenario:`
  - P1: Design decisions include rationale 
  - P1: `spec.md` uses normative language (MUST/SHOULD/MAY)
  - P1: Tasks have clear acceptance criteria
  - P1: Edge cases and error states are addressed

4. **Cross-artifact consistency issues**

  Compare artifacts against each other for contradictions, gaps, and ambiguities:
  - P0: Features listed in the proposal are not contradicted by design Non-Goals
  - P1: Normative behavior defined in design (e.g. fallback, error handling) is also captured in spec with scenarios
  - P1: Scope boundaries are consistent across proposal, specs, design, and tasks — no artifact claims something another excludes

5. **Commonality issues**

  Review artifacts for commonality with existing open and/or archived changes/specs:
  - Related existing specs
  - Existing shared components/services/utilities
  - Existing (`openspec/changes`) or archived (`openspec/changes/archived`) changes with similar functionality
  - Decision categories
    | Decision | Description |
    |----------|-------------|
    | Reuse | Use existing component as-is |
    | Extend | Extend existing component |
    | New (shared) | Create new, refactor into shared component |
    | New (dedicated) | Dedicated to this feature (document rationale) |

6. **Best-practice alignment issues**

  Review artifacts for decisions contrary to established best-practices:

  a. Generate narrowly-scoped search themes comparable to industry best practices based on:
    - The problem domain in `proposal.md` (e.g. "auto-remediation pipeline"); and
    - Key technical decisions in `design.md` (e.g. "3-category classification", "partial re-verification")

  b. You MUST **ask the user** the following questions before proceeding with a search:
    - "Are these search themes specific enough, or should we narrow the scope?"
    - "Are there particular standards, patterns, or technologies we should focus on?"

  c. Search the web for best practices on each selected theme. Use the search results as input for the next comparison step — do not adopt them as-is.

    > **IMPORTANT**: If web search is unavailable, notify the user, then present the user with the following options in a **NUMBERED** list and **ask the user** how to proceed:
    - Use model training knowledge as the research source
    - Enable web search and try again
    - Stop the search

  d. Compare research results against **each decision in design.md one by one**.

    > **IMPORTANT** Focus comparison on the dimensions most relevant to the project's domain and constraints.

    For each decision, evaluate the following:
    - P1: Does it align with industry standards?
    - P1: Are there missing perspectives? (e.g. side-effect verification, telemetry, explainability)
    - P2: Are there better patterns? (cite specific pattern names and sources if found
    - P0: **CRITICAL** Have alternative approaches been considered?
      - If a decision has no "alternatives" or "rejected patterns" section, suggest alternatives from research

  e. Present the user with a summary of the comparison results:
    > **IMPORTANT** Include the summary table in the scratchpad under `## Best-Practice Comparison`.
    ```
    | Decision | vs. Industry Standard | Alternatives | Gaps |
    |----------|----------------------|-------------|------|
    | D1: ... | Aligned / Room for improvement | Considered / Not considered (alt X, Y) | Missing aspects |
    ```

7. **Design issues**

  Review the design for common architectural concerns:
  - P0: **Separation of concerns**: Clear layer/class responsibility ownership
  - P0: **Dependencies**: Acyclic dependencies between components
  - P1: **Pattern selection**: Documented rationale for design pattern choices
  - P1: **Framework consistency**: Alignment with the existing codebase
  - P2: **Rejected patterns**: Documented rationale for considered but rejected patterns

8. **Propose remediation strategy** (MANDATORY)

  - **IF no issues were found**: Present the user with the following options as a **NUMBERED** list:
    - Commit the scratchpad and updated artifacts for `<change-name>`
      > **IMPORTANT** **IF** the user selects this option, you MUST use the `nhc-conventional-commit` skill to complete the commit.
    - Continue with `openspec-apply` to implement the change
    - Stop the refinement.

  - **IF one or more issues were found**:
    -  Present the user with a brief summary of the issues grouped in priority order, P0 first (see [Issue Priorities](#issue-priorities)):
      ```markdown
      ## P0
      1. <title>
        - Current: <issue-summary>
        - Proposed: <remediation-summary>
      2. <title>
      ...
      ## P1
      1. <title>
      ...
      2. <title>
      ...
      ## P2
      ...
    - Evaluate each of the following **IF** statements, present the user with a **NUMBERED** list of the remediation strategies following the true **IF** statements, then **ask the user** to select ONE choice by number:
      - Remediate all issues
      - Remediate major (P0) issues only
      - Remediate a selection of issues
      - Stop the refinement round.

9. **Execute remediation strategy** (MANDATORY)

  Artifact update rules:
  - **spec.md**: Normative content only. Background, comparisons, and rationale go in `proposal.md`
  - **spec.md size limit**: If approaching 400 lines, split into `details/*.md` with logical groupings per file
  - **tasks.md**: Implementation tasks only (no spec content)
  - **design.md**: Technical decisions only (comparisons and discussion go in `proposal.md`)
  - **proposal.md**: Update only when research-driven design changes affect Capabilities or What Changes
  - **Cross-artifact propagation**: After editing an artifact, EXPLICITLY verify that dependent artifacts are still consistent
  - **Minimal edits**: Edit only what's needed — no full rewrites

10. **Validate artifacts** (MANDATORY)

  You MUST validate artifact changes after one or more issues are addressed:
  ```bash
  openspec validate <change-name> --strict
  ```
  **IMPORTANT** If validation errors occur, fix them and re-run the validation test until it passes.

11. **Final status** (MANDATORY)

   When all issues are resolved, or the user decides to stop:
   ```bash
   openspec status --change "<name>"
   ```
   - Summarize the number of new, resolved, and remaining unaddressed issues
   - If unaddressed issues remain, note them as open in the scratchpad for future sessions

# Issue Status Labels
- Open: Not yet captured consistently in OpenSpec artifacts
- Needs refinement: Partially captured; artifacts still need clarifications/consistency work
- Consistent: Artifacts are aligned with current intended behavior (may still evolve as scope grows)
- Accepted: User accepted the artifact(s) as-is
  - The user MAY explicitly request this status for ALL priorities
  - You MAY suggest this status for ALL priorities, but the suggestion MUST be justified and it MUST be confirmed by the user before applying it
  - You MUST advise the user with a concise, factual argument to NOT select this status for P0 issues

# Scratchpad Rules
- You MUST use the scratchpad to track issue status and working decisions throughout the refinement process
- The "Last updated" date MUST be kept current
- You MUST list issues in order of priority, P0 first
- You MUST use the format `P<L>(i)`, where `L` is the level and `(i)` is the index, e.g. P0(0), P1(2), P2(3) (see [Issue Priorities](#issue-priorities))
- The issue status MUST reflect the state of the artifacts, not the implementation (see [Issue Status Labels](#issue-status-labels))
- The scratchpad is scoped per-change and persists across refinement sessions

# Scratchpad Format
```markdown
## <change-name> Scratchpad

Tracks openspec-refine issues and working decisions for `<change-name>` artifacts.
This is a working document, not a spec artifact.

Last updated: YYYY-MM-DD

## Status Legend
- **Open**: Not yet captured consistently in OpenSpec artifacts.
- **Needs refinement**: Partially captured; artifacts still need clarifications/consistency work.
- **Consistent**: Artifacts are aligned with current intended behavior (may still evolve as scope grows).
- **Accepted**: User accepted the affected artifact(s) as-is.

## Key References
- (List any external references used to resolve issues)

## Current Working Constraints / Decisions
- (List constraints or decisions affecting the current issue)

## Best-Practice Comparison
- (Include the best-practice comparison table from Step 6.e)

## Issue List

### P0(1): <title>
- Status: <status>
- Notes:
  - Clarify the current status.
- Artifacts touched:
  - `openspec/changes/<change-name>/...`
  - ...

### P1(1): <title>
...

### P2(1): <title>
...

## Open Questions (Need Clarification)
- (Questions needing user clarification)

```

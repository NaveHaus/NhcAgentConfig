---
name: openspec-refine
description: >
  A quality review skill that refines OpenSpec changes to implementation-ready quality.
  Performs cross-artifact consistency checks on spec.md/design.md/tasks.md,
  commonality analysis, and best-practice validation.
  Use after: (1) proposal creation (2) pre-implementation final review (3) spec quality improvement (4) design.md architecture review.
  Trigger words: refine, spec quality, spec review, design review, commonality check.
  Original: https://gist.github.com/shohu/a219c8137f2967d720552a304badeeee
---

# OpenSpec Refine

A review skill that refines OpenSpec changes to implementation-ready quality.

## Usage

This is a model-agnostic instruction file — it works with any LLM-powered coding tool (Claude Code, opencode, Cursor, Aider, etc.).

- **Claude Code**: Save as `~/.claude/skills/openspec-refine/SKILL.md` (global) or `.claude/skills/openspec-refine/SKILL.md` (per-project)
- **Other tools**: Load as a system prompt, custom instruction, or project-level prompt file according to your tool's convention

## Step 1: Context Collection

```bash
openspec list
ls openspec/changes/
```

Read the following artifacts for the target change:
- `proposal.md`, `tasks.md`, `design.md`, `specs/`
- Related existing specs in `openspec/specs/`

### Issue Link Check (Required)

Ask the user: "Is there a related GitHub Issue? (e.g. #42)"
- **If yes**: Record the issue number. In Step 4, add `Related: #<number>` (or `Closes: #<number>` if the change fully resolves it) at the top of each updated artifact.
- **If no**: Skip and continue.

### Related Skill/Convention Check (Required)

1. Check the project's instruction files (e.g. CLAUDE.md, AGENTS.md, .cursorrules, or equivalent) and any skill/prompt definitions
2. Identify existing skills or conventions matching the proposal's keywords
3. **Review the matching skill/convention** to confirm its patterns and rules
4. Verify the change follows established patterns; document any deviations with rationale

## Step 2: Analysis and Review

### Cross-Artifact Scope Consistency (Required)
- [ ] Features listed in proposal scope are not contradicted by design Non-Goals
- [ ] Normative behavior defined in design (e.g. fallback, error handling) is also captured in spec with scenarios
- [ ] Scope boundaries are consistent across proposal, specs, design, and tasks — no artifact claims something another excludes

### Required Checklist
- [ ] spec.md uses normative language (MUST/SHOULD/MAY)
- [ ] Each Requirement has at least one `#### Scenario:`
- [ ] Acceptance criteria are concrete (Given/When/Then preferred)
- [ ] Failure behavior is explicitly defined
- [ ] tasks.md is broken down into implementable granularity
- [ ] No hardcoded values in design — configuration values, paths, thresholds, and URLs have explicit externalization strategies (env vars, SSM, config files, constants, etc.) documented in design.md or tasks.md

### design.md Review (if present)
- [ ] **Separation of concerns**: Which layer/class owns what responsibility
- [ ] **Pattern selection**: Design patterns used and rationale for choosing them
- [ ] **Rejected patterns**: Patterns considered but not adopted, with reasons
- [ ] **Framework consistency**: Alignment with the existing codebase
- [ ] **Dependencies**: Dependency direction between components (no circular deps)
- [ ] **Commonality analysis**: Investigation of overlap with existing code

### When design.md Is Needed
Propose creating design.md if any of these apply but design.md is absent:
- Changes span multiple components or layers
- New patterns or architectural decisions are needed
- Deviation from existing design conventions

## Step 2.3: Best Practice Validation (MUST — do not skip)

May skip only when design.md does not exist. When design.md exists, always execute regardless of the number of Decisions.

### 2.3.1 Build Search Themes
From the problem domain in proposal.md (e.g. "auto-remediation pipeline") and key technical decisions in design.md (e.g. "3-category classification", "partial re-verification"), build search themes comparable to industry best practices.

**Human Guidance Checkpoint**: Before proceeding to research, present the search themes to the user and ask:
- "Are these search themes specific enough, or should we narrow the scope?"
- "Are there particular standards, patterns, or technologies you'd like me to focus on?"

This keeps token usage efficient by letting the user steer research toward the most relevant areas rather than casting a wide net.

### 2.3.2 Research Execution
Search the web for best practices on each theme (as refined by the user). Use the search results as input for the next comparison step — do not adopt them as-is.

**Fallback**: If web search is unavailable in your environment, use your training knowledge as the research source. Clearly note that the comparison is based on model knowledge rather than live search results, so the user can verify independently if needed.

### 2.3.3 Compare Against Current Design (MUST)
Compare research results against **each Decision in design.md one by one**. Focus comparison on the dimensions most relevant to the project's domain and constraints. For each Decision:
- Does it align with industry standards?
- Are there better patterns? (cite specific pattern names and sources)
- Are there missing perspectives? (e.g. side-effect verification, telemetry, explainability)
- **Have alternative approaches been considered?** (MUST)
  - e.g. Slack notification → AWS Chatbot / SNS+Email — different means for the same goal
  - If a Decision has no "alternatives" or "rejected patterns" section, suggest alternatives from research
  - A Decision with zero alternatives considered is a P0 Issue

Summarize comparison results:
```
| Decision | vs. Industry Standard | Alternatives | Gaps |
|----------|----------------------|-------------|------|
| D1: ... | Aligned / Room for improvement | Considered / Not considered (alt X, Y) | Missing aspects |
```

### 2.3.4 Propose Improvement Strategy (MUST when gaps exist)
When gaps are found:
1. List each gap as an independent improvement item (Current → Proposed → Source)
2. Present multiple integration strategies as options (MUST):
   - e.g. "A. Apply all improvements" / "B. High-impact N items only" / "C. Defer to next phase"
3. **Ask the user to choose a strategy** (MUST — do not just display text without asking)
4. Based on the chosen strategy, add applicable improvements as Issues in Step 3

## Step 2.5: Commonality Analysis (P1: Recommended)

May skip for small changes.

### Investigation Targets
- Related existing specs
- Existing shared components/services/utilities
- Past changes with similar functionality

### Decision Categories
| Decision | Description |
|----------|-------------|
| Reuse | Use existing component as-is |
| Extend | Extend existing component |
| New (shared) | Create new, place in shared package |
| New (dedicated) | Dedicated to this feature (document rationale) |

## Step 3: Present Improvement Proposals

```
### Issue: [Summary of the problem]
- Current: [What is missing or ambiguous]
- Proposed: [How to fix it]
- Priority: P0 (Required) / P1 (Recommended) / P2 (Nice-to-have)
```

## Step 4: Update Artifacts

After user approval, update the artifacts.

### Update Rules
- **spec.md**: Normative content only. Background, comparisons, and rationale go in proposal.md
- **spec.md size limit**: Split into `details/*.md` if approaching 400 lines
- **tasks.md**: Implementation tasks only (no spec content)
- **design.md**: Technical decisions only (comparisons and discussion go in proposal.md)
- **proposal.md**: Update only when research-driven design changes affect Capabilities or What Changes
- **Issue links**: If an issue number was specified in Step 1, add `Related: #<number>` or `Closes: #<number>` at the top of each updated file (after frontmatter or title)
- **Minimal edits**: Edit only what's needed — no full rewrites

## Step 5: Validation (Required)

```bash
openspec validate <proposal-id> --strict
```

If errors occur, fix and re-run until it passes.

## Quality Gate (Fail Conditions)

- spec.md contains background, comparisons, or discussion (normative content only)
- Acceptance criteria are vague (e.g. "works without issues")
- Requirements have no Scenarios
- design.md lists pattern names without selection rationale
- Multi-layer changes have no design.md
- Dependencies are circular or direction is unclear
- No investigation of overlap with existing specs/shared components (P1)
- Clear DRY violation — similar implementation exists with no justification for creating new (P1)
- Deviates from established project patterns without checking (P1)
- Configuration values, paths, thresholds, or URLs have no externalization strategy defined (P0)
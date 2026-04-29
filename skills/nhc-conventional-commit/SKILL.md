---
name: nhc-conventional-commit
description: Use the Conventional Commits v1.0.0 standard to generate git messages. Use to generate semantically consistent commit messages that support automated changelog generation and semantic versioning.
license: MIT
metadata:
  author: NaveHaus Consulting LLC
---

Write commit messages following Conventional Commits v1.0.0.

**Input**: Optionally specify the **Content to summarize**, e.g. a list of one or more files, raw text, or a description of the files that must be included.

## Requirements (MANDATORY)
- DO follow all steps EXACTLY.
- DO NOT add, remove or modify steps.
- DO NOT proceed with the final `git commit` without confirmation from the user.
- DO NOT execute any `git` commands other than those specified by this skill.

## Prerequisites
YOU MUST determine if there are changes to be committed based on the following decisions:

- Evaluate each of the following **IF** statements using the output of `git diff --name-only`, then follow the instructions for EXACTLY ONE true **IF** statement:
  - **IF** no output was returned:
    - Announce that no changes were found; then
    - **IF** the files to be committed can be clearly inferred from the content to be summarized, offer to `git add` those files.
    - **OTHERWISE** suggest that the user stage the files OR describe which files should be included. DO NOT GUESS.
  - **IF** untracked files were returned:
    - Announce that untracked files were found; then
    - **IF** the files to be committed can be clearly inferred from the content to be summarized, offer to `git add` those files.
    - **OTHERWISE** suggest that the user stage the files OR describe which files should be included. DO NOT GUESS.
  - **IF** an error was returned:
    - Summarize the error.
    - **IF** you understand the error, present the with a **NUMBERED** list of 1-3 remediation strategies, then **ask the user** how to proceed.
      > **IMPORTANT** Include an option to stop the commit.

## Required Message Format

```
<type>[optional scope]: <one-line description>

[optional body]

[optional footer(s)]
```

## Message Rules

1. Prefix the description with a type, optional scope in parentheses, `!` for breaking changes, colon, then space.
2. Description immediately follows the colon and space.
3. Body separated by one blank line, free-form.
4. Footers one blank line after body, token-separator-value format.
5. Indicate a breaking change by including `!` before `:`.

## Commit Types

| Type | Usage |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation changes only |
| `style` | Code formatting, whitespace (not for feature or fix changes) |
| `refactor` | Code restructuring (not for feature, fix, or style changes) |
| `perf` | Performance improvement changes |
| `test` | Adding/fixing tests |
| `build` | Build system, build configuration, dependencies |
| `ci` | CI/CD configuration |
| `chore` | Maintenance, tooling tasks |
| `revert` | Reverting previous commit |


## Determining the Message Type

- Does it add new functionality? `feat`
- Does it fix broken functionality? `fix`
- Does it only affect documentation? `docs`
- Does it only change code style/formatting? `style`
- Does it restructure code without changing behavior? `refactor`
- Does it improve performance? `perf`
- Does it add/modify tests? `test`
- Does it change build system or dependencies? `build`
- Does it change CI/CD configuration? `ci`
- Is it maintenance or tooling? `chore`
- Does it undo a previous commit? `revert`

## Best Practices

### Description (MANDATORY)
- You MUST clearly and succinctly summarize WHAT was done, not HOW or WHY.
- You MUST use no more than 50 characters if the resulting description accurately describes WHAT was done.
- You MAY use up to 72 characters if doing so would make the description clearer and/or more accurate.
- You MUST use an imperative mood ("add" not "added").
- DO NOT capitalize the first letter.
- DO NOT add a period at the end.

### Scope (OPTIONAL)
- The scope MUST be a defined subsystem or section of the codebase, not an individual file, function, concept, etc.
- DO include a scope if:
  - The subsystem or section of the codebase cannot be clearly inferred from the one-line description; or
  - The user provides an explicit scope.
- DO NOT include a scope if the user explicitly asks for it to be omitted.
- You MUST **ask the user** for clarification if the scope is unclear or ambiguous. DO NOT GUESS.
- You MUST use clear, consistent, and brief names for scopes, e.g. `feat(auth)`, `fix(api)`, `docs(user)`, `docs(dev)`.

### Body (OPTIONAL)
- You MUST include a body for complex changes that require more explanation than can be provided in the description.
- You MUST succinctly describe WHAT problem the change solves.
- You MUST explain WHY the change was made, not HOW or WHAT was changed.
- You MUST wrap the body at 72 characters per line.

### Footers (MANDATORY)
- DO NOT include footers unless explicitly requested by the user.

### General
- You MUST use a single-line (i.e. description only) commit message for simple or minor changes, e.g.:
  - No more than 20 single-line changes across 5 or fewer files
  - The addition of 2 or fewer files with a well-defined purpose that can be described in a single line
- You MUST **ask the user** if you are unsure about the commit message description or body format or content.
- You MUST **ask the user** if the content to be summarized is ambiguous or unclear.

## Steps (CRITICAL)
1. Determine what should be summarized in the commit message:
  - **IF** the content to be summarized was provided, proceed to Step 2.
  - **OTHERWISE** use the output of `git diff --staged` as the content to summarize

2. Prepare the commit:
  - Run `git log --oneline -10` to review and incorporate the style of recent commit messages
  - Determine the message type from the content to be summarized by following [Determining the Message Type](#determining-the-message-type)
  - Generate the commit message from the content to be summarized by following [Best Practices](#best-practices)
  - **IF** a single directory holds all changes to be committed, 

3. Complete the commit:
  > **IMPORTANT**: Every action in this step is **MANDATORY**.

  - Present the proposed `git commit` command to the user.
  - Present the user with a **NUMBERED** list of the following options, then **ask the user** how to proceed:
    1. Complete the commit with the exact command; or
    2. Enter a custom commit message; or
    3. Cancel the commit.
  - **IF** the user chooses to complete the commit, you MUST execute the `git commit` command exactly as presented to the user.
  - **OTHERWISE** acknowledge that the commit has been cancelled.

## References
- [Conventional Commits specification v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/#specification)
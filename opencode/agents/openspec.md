---
name: openspec
description: Use OpenSpec to plan, execute, and verify changes
mode: primary
temperature: 0.5
permission:
  read: allow
  edit: ask
  grep: allow
  list: allow
  bash:
    "ls *": allow
    "find *": allow
    "head *": allow
    "tail *": allow
    "openspec *": allow
  task: ask
  skill: allow
  lsp: allow
  todoread: allow
  webfetch: ask
  websearch: allow
---

You are an expert software engineer using the openspec tool and test-driven development (TDD) for specification, planning, executing, and verifying changes.
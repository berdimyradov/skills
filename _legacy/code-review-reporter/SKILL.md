---
name: code-review-reporter
description: Produce a private team-lead review memo for a PR, local diff, or patch, covering intent, risks, edge cases, test gaps, and suggested review comments.
---

Use this skill when the team lead asks to review a PR, summarize a diff, identify risks and missing tests, understand code-flow impact, or draft constructive review comments.

## Inputs

Required when available:

- PR diff, patch file, or local diff.
- PR title and description.
- Changed file list.

Optional context:

- Review comments, CI and test results, linked issue or user story, repository architecture docs, existing conventions, or Definition of Done.

## Evidence Workflow

1. Inspect the provided diff or local changes and identify what was added, modified, and removed.
2. Prefer available GitHub app tools or `gh` CLI for PR title, description, comments, changed files, checks, linked issues, and review state.
3. Infer intended behavior from PR description, linked issue, tests, and code changes.
4. Inspect changed files for correctness, architecture, maintainability, naming, security, performance, and test coverage.
5. Identify edge cases, user-story implications, dependency changes, and code-flow impact across module boundaries.
6. Draft review comments that are specific, constructive, and tied to evidence.
7. If evidence is unavailable, use user-provided data and state exactly what is missing and how it affects confidence.

## Output Contract

Produce a private `Code Review Report` with:

- Change summary: added, modified, removed.
- Intended behavior inferred from available evidence.
- Key risks and review findings, marked as blocking issues or suggestions.
- Testing gaps.
- Edge cases and affected user stories.
- Suggested paste-ready review comments.
- Optional dependency or flow impact notes when the change crosses module boundaries.
- Missing data and residual risk.

## Guardrails

- Reports only. Do not post PR comments, request changes, approve, merge, edit files, or create commits unless the user separately asks outside this reporting task.
- Cite file paths, line references when available, PR IDs, commits, tests, CI checks, review states/comments, or explicit missing-data notes.
- Keep comments actionable and precise.
- Do not fabricate repository conventions; cite conventions when found, otherwise mark them as assumptions.
- If no issues are found, state that clearly and list residual risk or test gaps.

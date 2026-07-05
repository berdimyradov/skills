---
name: task-health-reporter
description: Produce a private team-lead report on active work health, blocked PRs, stale work, ownership gaps, and review bottlenecks using Git and GitHub evidence.
---

Use this skill when the team lead asks what is blocked, stale, drifting from plan, missing ownership, waiting on review, or worth following up on before standup.

## Inputs

Required when available:

- Open GitHub PRs with authors, reviewers, review states, timestamps, labels, linked issues, and CI status.
- Recent Git commits and branches.

Optional context:

- Sprint goal, delivery plan, task tracker export, Definition of Done, known absences, incidents, support load, or team notes.

## Evidence Workflow

1. Inspect local Git history, branches, changed files, and recent commits for active work signals.
2. Prefer available GitHub app tools or `gh` CLI for PR metadata, review states, labels, comments, linked issues, and check results.
3. List active work from open PRs, branches, and linked tasks.
4. Identify stale work using PR age, last commit date, last review date, and last author response.
5. Identify blocked work from labels, failing checks, unresolved review threads, missing approvals, or explicit comments.
6. Flag unclear ownership, large risky PRs, missing test evidence, cross-module impact, migrations, config changes, and scope drift.
7. If evidence is unavailable, use user-provided data and state exactly what is missing and how it affects confidence.

## Output Contract

Produce a private `Task Health Report` with:

- Status snapshot: active PRs, blocked PRs, stale PRs with threshold used, and review bottleneck summary.
- Blocked or stale work table: work item, owner, evidence, risk, suggested TL action.
- Review bottlenecks: reviewer load, delayed first review, unresolved threads, or missing approvals.
- Scope or ownership risks: scope drift, unclear assignee, missing linked issue, large PR, or risky changed area.
- Recommended next actions for the TL.
- Missing data and confidence impact.

## Guardrails

- Reports only. Do not assign work, update trackers, post PR comments, merge code, or create commits unless the user separately asks outside this reporting task.
- Every item must cite a PR, commit, file path, timestamp, CI check, review state/comment, or explicit missing-data note.
- Contributor names are allowed, but state observable facts only and avoid ranking or productivity judgments.
- Do not assume a person caused a delay without review, calendar, workload, pairing, and support context.
- Phrase actions as recommendations for the team lead, not automated actions.

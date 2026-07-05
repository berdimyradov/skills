---
name: velocity-flow-risk-reporter
description: Produce a private team-lead report on delivery flow, throughput trends, WIP, batching risk, review latency, and delivery confidence from Git and GitHub evidence.
---

Use this skill when the team lead asks how velocity or delivery flow is trending, whether the team is slowing down, where work is getting stuck, or what to watch before planning or retro.

## Inputs

Required when available:

- Git commit history for the selected date range.
- GitHub PR lifecycle data: opened, first review, last update, approved, merged, closed.
- PR authors, reviewers, changed files, labels, linked issues, and CI state.

Optional context:

- Sprint boundaries, story points or estimates, team capacity notes, release milestones, planning notes, or known support load.

## Evidence Workflow

1. Define the reporting period and compare it with at least one prior period when available.
2. Inspect local Git for commits, branches, changed areas, and timing signals.
3. Prefer available GitHub app tools or `gh` CLI for PR lifecycle timestamps, review activity, reviewer load, labels, linked issues, and CI state.
4. Measure PR throughput, merge rate, open WIP, review latency, cycle-time signals, and rework indicators.
5. Detect batching risk from large PRs, long-lived branches, late merges, cross-module changes, or many files changed at once.
6. Attribute activity with context: authored PRs, reviewed PRs, merge timing, review load, and known caveats.
7. If evidence is unavailable, use user-provided data and state exactly what is missing and how it affects confidence.

## Output Contract

Produce a private `Velocity & Flow Risk Report` with:

- Delivery trend summary for the selected period.
- Flow metrics table: metric, current period, prior period, evidence, interpretation.
- WIP and batching risks.
- Review latency and reviewer load.
- Contributor attribution table with observed activity, evidence, and caveats.
- Recommended actions for planning, review process, WIP reduction, or risk reduction.
- Missing data and confidence impact.

## Guardrails

- Reports only. Do not assign work, update trackers, post PR comments, merge code, or create commits unless the user separately asks outside this reporting task.
- Cite PR IDs, commit hashes, timestamps, check results, review states/comments, or explicit missing-data notes.
- Treat velocity as a flow-risk signal, not a direct productivity score.
- Do not rank contributors by default or use commit count, line count, or PR count as proof of individual productivity.
- If squash merges, pair work, generated commits, absences, incidents, or support load may distort attribution, state that caveat.
- If estimates are missing, do not invent story-point velocity.

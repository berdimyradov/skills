---
name: teamlead-ai-skills
description: Route team-lead reporting requests to the right delivery, review, release, estimation, or coaching reporter skill using Git and GitHub evidence.
---

Use this suite router when a team lead asks for private engineering delivery reports based on Git commits, GitHub PRs, diffs, reviews, CI, release scope, estimates, or coaching signals.

## Choose The Reporter

- Use `task-health-reporter` for current active-work status: blocked PRs, stale work, unclear ownership, review bottlenecks, and standup follow-ups.
- Use `velocity-flow-risk-reporter` for delivery trends: throughput, WIP, review latency, batching risk, cycle-time signals, and flow confidence.
- Use `code-review-reporter` for one PR, local diff, or patch: correctness risks, edge cases, test gaps, architecture impact, and suggested review comments.
- Use `release-readiness-reporter` for release go/no-go guidance: included changes, blockers, watch items, CI evidence, deployment risk, and rollback concerns.
- Use `estimation-calibration-reporter` for estimate versus actual analysis: variance drivers, scope changes, rework, DoD clarity, and future calibration.
- Use `team-coaching-prep-reporter` for one-on-one or retro preparation: evidence-backed strengths, friction points, caveats, coaching questions, and support actions.

## Shared Operating Rules

- Produce reports only. Do not post PR comments, assign tasks, update trackers, merge code, create commits, or make people-management decisions unless the user separately asks for that action outside this reporting task.
- Treat GitHub as the v1 PR platform and local Git history as the default local evidence source.
- Collect evidence when possible: inspect local Git first, then prefer available GitHub app tools or `gh` CLI for PR metadata. If a source is unavailable, use user-provided data and state the confidence impact.
- Every concrete finding must cite a PR, commit, file path, timestamp, CI check, review state/comment, or an explicit missing-data note.
- Contributor names are allowed for accountability and coaching, but observations must be factual, contextual, and non-ranking by default.
- Separate observed facts from interpretation. Use uncertainty language for inferences and include caveats for missing calendar, workload, support, pairing, or planning context.

## Default Report Shape

Use a private decision memo unless the selected reporter defines a more specific output contract:

- Executive summary.
- Evidence snapshot.
- Findings with fact, evidence, interpretation, confidence, caveat, and recommended TL action.
- Risks.
- Recommended next actions for the team lead.
- Follow-up questions and missing-data notes.

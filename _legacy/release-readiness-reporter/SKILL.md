---
name: release-readiness-reporter
description: Produce a private go/no-go release readiness memo from release scope, merged PRs, open risks, CI, migrations, config changes, and deployment evidence.
---

Use this skill when the team lead asks whether a release can ship, what changed since the last release, what blockers remain, or what to verify before deployment.

## Inputs

Required when available:

- Release branch, tag range, or commit range.
- Merged PRs included in the release.
- Open PRs or known blockers.
- CI check results.

Optional context:

- Release scope or milestone, deployment plan, rollback plan, QA notes, migration list, feature flags, config changes, or stakeholder notes.

## Evidence Workflow

1. Inspect local Git for the release branch, tag range, commits, changed files, migrations, config changes, and risky areas.
2. Prefer available GitHub app tools or `gh` CLI for merged PRs, open blockers, PR review completeness, checks, labels, timestamps, and linked issues.
3. Summarize included PRs and commits by user-facing capability, technical change, migration, config, and risk area.
4. Check CI status and identify missing, failing, skipped, or stale checks.
5. Flag unreviewed, lightly reviewed, recently merged, or high-risk changes, especially migrations, infrastructure, authentication, billing, data model, or external integrations.
6. Compare release contents with declared scope and identify scope drift.
7. If evidence is unavailable, use user-provided data and state exactly what is missing and how it affects confidence.

## Output Contract

Produce a private `Release Readiness Report` with:

- Recommendation: `Go`, `No-Go`, or `Conditional Go`.
- Included changes table: area, PRs/commits, risk.
- Blockers with evidence and required TL action.
- Watch items with evidence and context.
- Testing and CI evidence.
- Deployment and rollback concerns.
- Final verification checklist.
- Missing data and confidence impact.

## Guardrails

- Reports only. Do not deploy, modify release branches, merge PRs, update trackers, post comments, or create commits unless the user separately asks outside this reporting task.
- Cite PRs, commits, CI checks, migration files, config files, timestamps, review states/comments, or explicit missing-data notes.
- Do not call a release ready if required evidence is missing; use conditional language.
- Separate blockers from watch items.
- Keep the recommendation tied to evidence and caveats.

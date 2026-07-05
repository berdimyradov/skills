# Skills

Personal agent skills for Codex and compatible local agent environments.

This repository is a small skill workspace. Active skills live in `skills/`.
Retired or superseded skills live in `_legacy/` for reference.

## Active Skills

| Skill | Purpose |
| --- | --- |
| [`spec-driven-agent-loop`](skills/spec-driven-agent-loop/SKILL.md) | Runs Specinator, a local-only Spec Driven AI Loop for applying ready OpenSpec changes through stacked GitHub draft PRs, structured review, review-fix workers, resume, and archive flows. |

## Specinator

Specinator is the user-facing workflow provided by
[`spec-driven-agent-loop`](skills/spec-driven-agent-loop/SKILL.md). It is built
for repositories that already use OpenSpec and GitHub pull requests.

Use it when you want an agent to:

- Apply one or more ready OpenSpec changes in order.
- Create one draft GitHub PR per OpenSpec change.
- Use stacked branches for ordered multi-change runs.
- Run validation, tests, typechecks, GitHub checks, and structured AI review.
- Apply required review fixes through isolated worker contexts.
- Resume a long-running PR stack from a local state file.
- Create post-merge OpenSpec archive PRs after a human has merged the implementation PRs.

Specinator does not create OpenSpec proposals, merge PRs, deploy code, or make
product decisions. It stops when it needs human judgment.

## Usage

Install or point your agent environment at the skill directory:

```text
skills/spec-driven-agent-loop/
```

Then invoke the skill by name or by using the Specinator workflow name.

```text
Use Specinator to apply add-project-dashboard.
```

```text
Use Specinator to apply add-project-dashboard improve-dashboard-filters in order.
```

```text
Use Specinator to resume 20260705-143022-add-project-dashboard.
```

```text
Use Specinator to archive 20260705-143022-add-project-dashboard.
```

The skill supports three explicit modes:

- `apply <change...>`: apply one or more ready OpenSpec changes.
- `resume <run-id>`: resume from `.scratch/agent-workflows/<run-id>.md`.
- `archive <run-id>`: create archive PRs after implementation PRs are merged.

## Requirements

Specinator expects the target repository to have:

- OpenSpec installed and configured.
- A clean Git worktree before `apply`.
- GitHub access through `gh`.
- Push and PR creation permissions.
- Local validation and check commands such as `openspec`, `pnpm test`, and `pnpm typecheck`.
- Isolated worker or subagent support for implementation and review-fix work.

The workflow is intentionally local-first. Its run state is written to
`.scratch/agent-workflows/<run-id>.md` in the target repository and should not be
committed.

## Repository Layout

```text
skills/
  spec-driven-agent-loop/
    SKILL.md
    agents/openai.yaml

_legacy/
  teamlead-ai-skills/
  task-health-reporter/
  velocity-flow-risk-reporter/
  code-review-reporter/
  release-readiness-reporter/
  estimation-calibration-reporter/
  team-coaching-prep-reporter/
```

## Legacy Skills

The `_legacy/` directory contains the earlier team-lead reporting suite. Those
skills produced private, evidence-backed reports for delivery health, review
quality, release readiness, estimation calibration, and coaching preparation.

They remain in the repository for reference, but the active catalog is the
`skills/` directory.

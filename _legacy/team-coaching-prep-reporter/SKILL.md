---
name: team-coaching-prep-reporter
description: Produce private team-lead one-on-one or retro prep notes from evidence-backed Git and GitHub collaboration, strengths, friction points, and support signals.
---

Use this skill when the team lead asks for one-on-one notes, retro topics, collaboration signals, support needs, or evidence-backed strengths and friction points for a person or team.

## Inputs

Required when available:

- Contributor PRs, commits, and reviews for a selected period.
- PR lifecycle timestamps and review comments.

Optional context:

- Role expectations, known focus areas, support load, incident load, mentoring responsibilities, previous one-on-one notes, team goals, sprint goals, absences, or pairing context.

## Evidence Workflow

1. Define the person or team, reporting period, and intended conversation context.
2. Inspect local Git for authored commits, changed areas, timing, and collaboration signals where available.
3. Prefer available GitHub app tools or `gh` CLI for authored PRs, reviewed PRs, merged work, review cycles, comments, timestamps, and affected areas.
4. Identify strengths from evidence: ownership, helpful reviews, delivery consistency, risk surfacing, testing habits, documentation, mentoring, or cross-team coordination.
5. Identify friction points from evidence: blocked PRs, repeated review themes, unclear requirements, test gaps, large batching, delayed responses, or dependency waits.
6. Add context caveats for workload, support duty, meetings, pairing, squash merges, absences, generated commits, or missing data.
7. Convert observations into coaching questions and possible support actions for the TL.

## Output Contract

Produce a private `Team Coaching Prep Report` with:

- Context: person or team, period, data sources.
- Evidence-backed strengths.
- Friction points or risks.
- Caveats and missing context.
- Suggested conversation questions.
- Possible support actions.
- Missing data and confidence impact.

## Guardrails

- Reports only. Do not send messages, update HR systems, assign tasks, post PR comments, or make people-management decisions unless the user separately asks outside this reporting task.
- Cite PRs, commits, review comments, timestamps, file paths, or explicit missing-data notes.
- This is not a performance review generator by default.
- Do not rank contributors or infer attitude, effort, motivation, or skill from GitHub data alone.
- Keep tone private, factual, contextual, and coaching-oriented.
- If asked for performance assessment, state the limits of GitHub-only evidence.

---
name: estimation-calibration-reporter
description: Produce a private team-lead report comparing estimates with actual delivery evidence, variance drivers, DoD clarity, and future calibration actions.
---

Use this skill when the team lead asks why an estimate missed, how to calibrate future estimates, whether scope grew, which assumptions were wrong, or what to change in planning.

## Inputs

Required when available:

- Original estimate or planning assumption.
- Related PRs and commits.
- Date range from active start to completion.
- Review and merge history.

Optional context:

- Story points, T-shirt size, acceptance criteria, Definition of Done, confidence vote, sprint burndown, capacity notes, tracker comments, support load, or dependency notes.

## Evidence Workflow

1. Capture the original estimate, sizing method, confidence, assumptions, and Definition of Done when available.
2. Inspect local Git for related commits, changed files, timing, rework, test additions, and scope indicators.
3. Prefer available GitHub app tools or `gh` CLI for PR lifecycle, review cycles, comments, linked issues, timestamps, and merge history.
4. Compare planned scope with actual changed files, PRs, commits, review cycles, completion timeline, and test evidence.
5. Identify variance drivers: hidden complexity, rework, review churn, dependency delay, unclear DoD, test gaps, late scope change, or external blockers.
6. Distinguish estimation error from execution disruption and note the uncertainty level: rough sizing, committed sprint work, or near-release completion.
7. If the original estimate is unavailable, reconstruct from evidence with low confidence and state what is missing.

## Output Contract

Produce a private `Estimation Calibration Report` with:

- Estimate versus actual summary: estimate, assumptions, actual PRs/commits, elapsed time, and review cycles.
- Evidence timeline table: date, event, evidence.
- Main variance drivers.
- DoD and scope clarity assessment.
- Calibration recommendations for similar future work.
- Planning questions for next time.
- Missing data and confidence impact.

## Guardrails

- Reports only. Do not update estimates, trackers, sprint boards, PRs, or commits unless the user separately asks outside this reporting task.
- Cite estimates, PRs, commits, review dates, completion dates, file paths, comments, or explicit missing-data notes.
- Do not blame individuals for estimate misses from Git or GitHub data alone.
- Use uncertainty language when planning context is incomplete.
- Do not treat commit count, line count, or PR count as proof of individual productivity.

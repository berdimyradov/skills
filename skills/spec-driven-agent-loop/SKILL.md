---
name: spec-driven-agent-loop
description: Orchestrate Specinator, the user-facing workflow for the personal Spec Driven AI Loop (SDAL) in this repo: apply one or more ready OpenSpec changes in order, create stacked GitHub draft PRs, run structured high-reasoning review, auto-apply required review fixes with isolated low-reasoning workers, resume long-running PR stacks, and create post-merge OpenSpec archive PRs. Use when the user asks for Specinator, SDAL, spec-driven AI loop, applying OpenSpec changes through PRs, resuming the OpenSpec PR workflow, or archiving merged OpenSpec changes.
---

# Specinator

Specinator is the user-facing name for the Spec Driven AI Loop (SDAL): a repo-local, local-only orchestrator for turning apply-ready OpenSpec changes into reviewed GitHub PRs. It composes existing OpenSpec skills, Git, `gh`, local checks, GitHub checks, and isolated workers.

Keep v1 local-only:

- Skill path: `.agents/skills/spec-driven-agent-loop/SKILL.md`
- State path: `.scratch/agent-workflows/<run-id>.md`
- Do not modify `.gitignore` for SDAL.
- Do not commit SDAL skill files or workflow state files.

## Modes

Use explicit modes only:

- `apply <change...>`: apply one or more ready OpenSpec changes in the given order.
- `resume <run-id>`: resume from `.scratch/agent-workflows/<run-id>.md`.
- `archive <run-id>`: after the user manually merges implementation PRs, create OpenSpec archive PRs.

Do not create proposals in this skill. Use `openspec-explore` and `openspec-propose` for proposal work.

## Core Policy

- One OpenSpec change equals one GitHub PR.
- Ordered multi-change runs use stacked branches by default.
- Implementation PRs start as draft.
- Humans merge all implementation PRs and archive PRs.
- SDAL never runs `gh pr merge`.
- Stop on the first failed ordered change, preserving completed PRs.
- Require isolated worker contexts. If subagent/worker tools are unavailable, stop; do not fall back to same-thread implementation.
- Coordinator should run with high reasoning. Workers use low reasoning unless the user explicitly overrides.

## Naming

Use `sdal/` branches.

Single change:

```text
sdal/openspec-<change-name>
```

Ordered batch:

```text
sdal/openspec-01-<change-a>
sdal/openspec-02-<change-b>
sdal/openspec-03-<change-c>
```

Archive:

```text
sdal/archive-openspec-<change-name>
```

Commit messages:

```text
feat(openspec): implement <change-name>
fix(openspec): address review for <change-name>
chore(openspec): archive <change-name>
```

## Preflight

Before `apply`, require a clean worktree and GitHub readiness. Refuse dirty worktrees except the active `.scratch/agent-workflows/<run-id>.md` on `resume`.

Run:

```bash
git rev-parse --show-toplevel
git status --short
git remote -v
git fetch origin
gh auth status
gh repo view --json nameWithOwner,url
openspec list --json
```

Also verify subagent worker support is available. If GitHub auth, fetch, push, PR creation, or comment capability is unavailable, stop before implementation. Do not create local-only PR simulations.

Default checks:

```bash
openspec validate "<change>" --strict
pnpm test
pnpm typecheck
gh pr checks "<pr>" --watch
```

If GitHub has no checks configured, record "remote checks unavailable" in the PR comment and state file.

## State File

Create `.scratch/agent-workflows/<run-id>.md` at the start of `apply`. Use a deterministic run id such as:

```text
YYYYMMDD-HHMMSS-<first-change>
```

The state file is local-only and ignored. Update it after every durable action.

Minimum state:

```markdown
# SDAL Run: <run-id>

- Mode: stacked | single
- Base branch: main
- Archive mode: pr
- Status: applying | blocked | ready-for-human | archiving | complete

| Order | Change | Branch | Base | PR | Status | Review Pass | Archive PR |
|---:|---|---|---|---|---|---:|---|
| 1 | change-a | sdal/openspec-01-change-a | main | #12 | ready-for-human | 2 | |
```

Use explicit statuses:

```text
not-started
applying
implemented
draft-pr-created
reviewing
fixing-review
ready-for-human
merged
archive-pr-created
blocked
```

`resume` must be idempotent:

- Reuse recorded branches, PRs, and comments.
- Update existing PR bodies/comments instead of creating duplicates.
- Stop if local state and GitHub state disagree.

## Apply Flow

For each change in order:

1. Determine branch/base.
   - Single change base: `main`.
   - First stacked change base: `main`.
   - Later stacked change base: previous SDAL branch.

2. Validate before work:

   ```bash
   openspec validate "<change>" --strict
   ```

3. Create or checkout the SDAL branch from the intended base.

4. Spawn one isolated low-reasoning implementation worker for this change.

5. Wait for the worker, then inspect changes.

6. Validate and check:

   ```bash
   openspec validate "<change>" --strict
   pnpm test
   pnpm typecheck
   ```

7. Commit:

   ```bash
   git add <changed files>
   git commit -m "feat(openspec): implement <change>"
   ```

8. Push branch and create a draft PR.

9. Run review flow.

10. Continue to the next change only after the current PR is `ready-for-human`.

## Implementation Worker Prompt

Spawn a `worker` with low reasoning. Pass only the current change, repo path, branch context, and check policy. Do not pass previous worker transcripts unless needed for a concrete blocker.

```text
Use the openspec-apply-change skill.

Repo: <repo-root>
OpenSpec change: <change>
Branch: <branch>
Base: <base>

Implement only this OpenSpec change.

Required workflow:
- Run `openspec status --change "<change>" --json`.
- Run `openspec instructions apply --change "<change>" --json`.
- Read every context file returned by the apply instructions.
- Read proposal, design, specs, and tasks when those files are present.
- Implement pending tasks only for this change.
- Keep edits minimal and scoped.
- Update tasks.md checkboxes as tasks are completed.
- Run targeted tests where practical.
- Do not run `openspec archive`.
- Do not commit.
- Do not push.
- Do not create or edit PRs.
- Do not implement or prepare other OpenSpec changes.
- Preserve unrelated user changes.

If a task is unclear, validation cannot pass, or implementation reveals a spec/design issue, stop and report the blocker instead of guessing.
```

## PR Body

Create draft PRs with a deterministic body:

```markdown
## OpenSpec Change

- Change: `<change>`
- Proposal: `openspec/changes/<change>/proposal.md`
- Design: `openspec/changes/<change>/design.md`
- Tasks: `openspec/changes/<change>/tasks.md`

## Stack

- Base: `<base>`
- Depends on: `<previous-branch-or-none>`
- Order: `<n>/<total>`

## Verification

- [x] `openspec validate <change> --strict`
- [x] `pnpm test`
- [x] `pnpm typecheck`
- [ ] `gh pr checks --watch`

## AI Review Protocol

Review findings are tracked in a structured PR comment titled `AI Review Findings`.
```

## Review Flow

Run a high-reasoning structured review after the draft PR exists. Use the existing `review` skill concepts, but normalize output into the SDAL review format.

Compare against the PR base using a three-dot diff:

```bash
git diff <base>...HEAD
git log <base>..HEAD --oneline
```

Read relevant standards sources such as `AGENTS.md`, `CONTEXT.md`, `docs/adr/`, `eslint`/`tsconfig`/formatter config if present. Read OpenSpec proposal, design, tasks, and delta specs for the change.

Always create or update exactly one PR comment headed:

```markdown
## AI Review Findings
```

Required sections:

```markdown
Status: passed | required-fixes | needs-human-judgment
Review pass: 1 | 2
OpenSpec change: `<change>`
Compared against: `<base>`

### Required Fixes

- [ ] RF-001
  - Source: Standards | Spec
  - File:
  - Problem:
  - Required change:
  - Evidence:

### Suggestions

- [ ] SG-001
  - Source:
  - Rationale:

### Questions / Needs Human Judgment

- Q-001:

### Verification

- [x] `openspec validate <change> --strict`
- [x] `pnpm test`
- [x] `pnpm typecheck`
- [x] `gh pr checks --watch` or `remote checks unavailable: <reason>`
```

If no findings exist, still post/update the comment with `Status: passed` and `None.` under every finding section.

## Review-Fix Flow

Auto-apply `Required Fixes` only. Ignore `Suggestions` by default. Stop and ask the user if there are `Questions / Needs Human Judgment`.

Review loop:

```text
review pass 1
  no Required Fixes and no Questions -> remote checks -> mark PR ready
  Required Fixes -> low-reasoning fix worker -> checks -> review pass 2
  Questions -> stop

review pass 2
  no Required Fixes and no Questions -> remote checks -> mark PR ready
  Required Fixes or Questions -> stop for user
```

After a successful fix worker:

```bash
openspec validate "<change>" --strict
pnpm test
pnpm typecheck
git add <changed files>
git commit -m "fix(openspec): address review for <change>"
git push
```

Then run review pass 2.

## Review-Fix Worker Prompt

Spawn a `worker` with low reasoning.

```text
Repo: <repo-root>
PR: <pr-number-or-url>
OpenSpec change: <change>
Branch: <branch>

Read the PR body and the canonical `AI Review Findings` PR comment.

Implement only unchecked `RF-*` Required Fixes. Do not implement Suggestions. Do not answer Questions by guessing.

Constraints:
- Keep edits minimal and scoped to the required fixes.
- Preserve unrelated user changes.
- Do not run `openspec archive`.
- Do not commit.
- Do not push.
- Do not create or edit PRs.
- Do not add new product scope beyond the required fixes.

After fixing, run the narrowest relevant check and report changed files plus commands run. If any RF item is unclear or conflicts with the OpenSpec artifacts, stop and report the blocker.
```

## PR Readiness

Mark a PR ready only when all are true:

- `openspec validate <change> --strict` passed.
- `pnpm test` passed.
- `pnpm typecheck` passed.
- `gh pr checks <pr> --watch` passed, or no remote checks are configured and that is recorded.
- Latest `AI Review Findings` status is `passed`.
- No unresolved `Required Fixes`.
- No `Questions / Needs Human Judgment`.

Then run:

```bash
gh pr ready "<pr>"
```

Update the state file to `ready-for-human`. Do not merge.

## Stacked Resume

On `resume <run-id>`:

1. Read state.
2. Verify local branches, remote branches, PR numbers, PR bases, draft/ready state, and the canonical review comment.
3. If an earlier PR is merged, update the next branch:

   ```bash
   git checkout main
   git pull --ff-only origin main
   git checkout <next-branch>
   git rebase main
   git push --force-with-lease
   gh pr edit <next-pr> --base main
   ```

4. Leave deeper stacked PRs based on the immediately previous unmerged SDAL branch.
5. Rerun validation, checks, and review gates for any rebased branch before declaring it ready.

Stop on rebase conflicts or state mismatches.

## Archive Flow

Run `archive <run-id>` only after the user has manually merged implementation PRs.

For each merged change:

1. Verify the implementation PR is merged into `main`.
2. Checkout updated `main`.
3. Create archive branch:

   ```bash
   git checkout main
   git pull --ff-only origin main
   git checkout -b "sdal/archive-openspec-<change>"
   ```

4. Archive and validate:

   ```bash
   openspec archive "<change>" --yes
   openspec validate --all --strict
   pnpm test
   pnpm typecheck
   ```

5. Commit and push:

   ```bash
   git add <archive/spec files>
   git commit -m "chore(openspec): archive <change>"
   git push -u origin "sdal/archive-openspec-<change>"
   ```

6. Create an archive PR. Do not merge it.

Archive PR body should include:

```markdown
## OpenSpec Archive

- Change: `<change>`
- Implementation PR: `<pr>`

## Verification

- [x] `openspec archive <change> --yes`
- [x] `openspec validate --all --strict`
- [x] `pnpm test`
- [x] `pnpm typecheck`
```

Update state to `archive-pr-created`.

## Failure Policy

For ordered multi-change runs:

- Preserve completed PRs.
- Stop on the first validation, worker, review, check, push, PR, rebase, or archive failure that cannot be repaired safely.
- Do not continue to later changes after a failed earlier change.
- Do not roll back branches or PRs without explicit user instruction.
- Summarize:
  - completed changes,
  - blocked change and phase,
  - not-started changes,
  - exact commands/checks that failed,
  - next safest resume command.

Configured check failures may be repaired within a bounded budget of two attempts when the cause is clearly inside the current change or stale generated/test artifacts. Stop if the failure requires product judgment, secrets, destructive action, or a spec/design update.

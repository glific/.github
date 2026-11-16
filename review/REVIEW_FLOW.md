# Glific review flow

Applies to `glific`, `glific-frontend` and `glific-web-channel`.

```
local pre-push review  →  push / open PR  →  PR review bot  →  human review  →  merge
```

## 1. Local pre-push review (author)

Run the `make-branch-ready-for-review` skill in Claude Code. It:

1. Runs the repo's local checks (lint, types, format, tests, coverage gate).
2. Runs the repo's local review agent over the branch diff.
3. Commits and pushes, then loops until CI is green and every PR bot comment is addressed.

## 2. PR review bot (automated)

`.github/workflows/pr-review.yml` in each repo calls the shared workflow
`glific/.github/.github/workflows/claude-pr-review.yml`, which runs Claude Code with
`review/pr-bot.md`. It runs on non-draft, non-fork PRs, posts comments only, and **never blocks a
merge** (it is not a required status check).

## 3. Human review

Unchanged: the repo's existing branch protection and required approvals.

## Who checks what

| | Local reviewer (pre-push) | PR bot |
|---|---|---|
| Owns | Repo conventions from `CLAUDE.md`, idiomatic code, lint/format/type fixes, test coverage, line-level correctness | Security, breaking API/schema/contract changes, migrations and data safety, cross-file correctness, missing tests for new behaviour, PR scope vs description |
| Must not | Post on the PR | Comment on style, formatting, naming, anything CI enforces, or conventions the local reviewer owns |

## Changing the review

- Shared behaviour: edit `review/pr-bot.md` or the reusable workflow in `glific/.github`. Every
  repo picks it up on the next PR.
- Repo-specific focus: edit the `repo_rules` input in that repo's `pr-review.yml`. Keep it to path
  rules and stack hints; do not copy the shared prompt.

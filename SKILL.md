---
name: spec-to-pr-workflow
description: Use when starting a refactor or multi-PR piece of work that needs a spec and tracker cards, when picking up a tracker card to turn into a pull request, when about to execute an implementation plan, or when about to push a branch and open a pull request
---

# Spec to PR workflow

## Overview

A fixed sequence of review gates between an idea and a merged pull request. The
owner decides at four points; everything else runs without asking.

**Each gate is cheap before the next step and expensive after it.** A gate that
was not run is reported as not run. It is never dropped silently and never
replaced by a later gate ("CI will catch it", "the final review covered it").

**REQUIRED SUB-SKILLS:** superpowers:brainstorming, superpowers:writing-plans,
superpowers:subagent-driven-development, and the `code-review` skill.

## Project settings

Read the `## Delivery workflow` section of the project's CLAUDE.md for: base
branch, branch naming, pre-push commands, how to run them when the local
runtime is the wrong version, tracker board and list names, where specs and
plans are saved. If the section is missing, ask the owner for these once and
offer to add it.

## Part A: idea to tracker cards (once per piece of work)

1. Owner states the goal and the criteria.
2. Ask scoping questions one at a time, each with a recommendation. **Owner decides.**
3. Update the base branch locally. Audit and measure only on it.
4. Audit with independent agents, one per criterion group. Every claim needs file and line.
5. Present the design. **Owner decides.**
6. Write the spec and the card list.
7. Spec review by independent agents, one aspect each: factual accuracy, card
   decomposition, behaviour preservation, consistency and scope.
8. Fix every finding. Record the review in the spec.
9. Agree how cards batch into PRs and how much is committed. **Owner decides.**
10. Create the tracker cards. **Owner decides.**

## Part B: card to merged PR (every PR)

| # | Step | Gate |
|---|------|------|
| 1 | Pick the next card; check its dependencies are merged and its open decisions are answered. Move the card to "in progress". Branch from the fresh base branch. | |
| 2 | Write the plan in tasks that can each be tested alone. Prototype the risky part. | |
| 3 | **Plan review: two independent agents.** One checks the plan against the spec and card (complete, nothing extra, behaviour preserved). One checks the design choices and that each task is testable alone. Fix the findings. | Plan review |
| 4 | Show the owner the plan **and** the reviewers' findings. | **Owner decides** |
| 5 | Build one task at a time: fresh implementer, then a separate reviewer, then fixes and re-review. **Every task gets a reviewer, including a task you do yourself.** | Task review |
| 6 | Whole-branch review by an agent on the most capable model. One fix agent for all findings; verify the fixes yourself. | Branch review |
| 7 | Run the `code-review` skill at high effort with `--fix` on the branch. This is in addition to step 6. | Code review |
| 8 | Run every pre-push command from the project settings. If the local runtime cannot run one, use the project's container or other documented way. | Pre-push checks |
| 9 | Open-PR check: read the **diff** of every open PR against the base branch, not its title or file list. Look for the same files, and for anything this branch freezes or counts (baselines, snapshots, allowlists, generated files). A conflict goes to the owner with a recommended merge order. | Open-PR check |
| 10 | Report the gate table (below) and ask to push. | **Owner decides** |
| 11 | Push, open the PR against the base branch with the gate table in the description, move the card to "in review", add the PR link. Read the CI result. | |
| 12 | If the base branch moves: rebase, regenerate anything frozen, rerun the tests, push with `--force-with-lease`, update the description. | |
| 13 | Owner reviews and merges. Never merge and never push to the base or production branch. | **Owner decides** |
| 14 | Move the card to "done". Say in one or two lines what is next. | |

### Earlier approval

An earlier "push when it's done" or "just start" is the owner's decision at
step 4 or step 10. It does not remove a gate. Run the gates first.

- If every gate ran and is clean: act on the earlier approval without waiting,
  and still send the plan findings or the gate table.
- If a plan reviewer found something that changes scope or behaviour, if the
  open-PR check found a conflict, if a gate could not run, or if the code
  review left a finding for the owner: stop and wait for the owner. The
  earlier approval did not cover it.

If a fix after step 7 changes code, rerun step 8 before the push.

## Gate table (required in the step 10 report and in every PR description)

Fill in every row. "Not run" needs a reason and stays visible.

```markdown
| Gate | Result |
|------|--------|
| Plan review (2 agents) | <findings fixed / none> |
| Task review | <n of n tasks reviewed> |
| Branch review | <findings fixed / none> |
| Code review (high) | <n found, n fixed, n left for owner: ...> |
| Pre-push checks | <each command: passed, where it ran / not run, why> |
| Open-PR check | <PRs read; conflicts or none> |
| Not verified | <what, or "nothing"> |
```

## Common mistakes

| Thought | Reality |
|---------|---------|
| "I prototyped it and re-read the plan" | The author cannot review their own plan. Two independent agents. |
| "One plan reviewer covers both" | Spec fit and design are different questions. Two agents. |
| "This task is small, I'll do it myself, no reviewer" | Small unreviewed tasks are where the gaps were found. |
| "The branch review was clean, code review is a repeat" | They find different things. Run both, before the PR. |
| "CI runs the tests anyway" | The project asks for local checks before the push. Use the container. |
| "I looked at the open PRs' file lists" | A one-line change in an unrelated-looking PR can break a frozen baseline. Read the diffs. |
| "The owner already said push" | That answers step 10. The gates before it still run. |

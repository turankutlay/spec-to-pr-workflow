---
name: spec-to-pr-workflow
description: Use when starting a refactor or multi-PR piece of work that needs a spec and tracker cards, when picking up a tracker card or task to turn into a pull request, when about to execute an implementation plan, or when about to push a branch and open a pull request
---

# Spec to PR workflow

## Overview

The owner hands over a task and gets back a pull request that is tested, run,
reviewed for quality and ready to merge, with the evidence in one table.

This skill fixes three things: **what done means**, **the quality the code must
have**, and **when to stop for the owner**. How you get there is your choice:
use your own judgment and the installed skills (planning, subagents, code
review). Do not stop to ask outside the listed stop points.

A check that did not run is reported as not run, with the reason. It is never
dropped and never replaced by a later check ("CI will catch it").

## Project settings

Read the `## Delivery workflow` section of the project's CLAUDE.md: base
branch, pre-push commands and how to run them when the local runtime is the
wrong version, how to start the application and its clients, test accounts,
test environment, tracker, and how the project rates risk. If the section is
missing, ask the owner once and offer to add it.

## Risk

Rate the task before starting, say the rating and why, and use the higher
level when unsure.

| Level | Typical signs |
|-------|---------------|
| Low | Docs, tooling, CI, dead-code removal, mechanical moves fully covered by tests |
| Medium | Changes the structure of code the application runs; behaviour must stay the same and tests cover it |
| High | Money, authentication, data migration or deletion, a public API contract, or code without test coverage |

## Code quality (every PR that contains code, at every risk level)

Risk changes how many review rounds happen. It never lowers the quality bar.

The code follows nine rules:

- **SOLID, KISS, DRY, YAGNI.** When they disagree, KISS and correct behaviour
  win over DRY: reuse an existing function only if its behaviour, messages and
  cost fit the new caller as they are.
- **Fail loudly.** An error never reads as success; a check that cannot run fails.
- **Least surprise.** Messages, flags and defaults say what the code really
  does; unknown input is rejected, not ignored.
- **Measure the cost.** A change to a script, query or hot path has its runtime
  measured before and after.
- **Behaviour preservation** (refactors). A test that pins current behaviour
  passes before the code moves and passes unchanged after.
- **Reversibility.** The PR says how to roll it back and what breaks if only
  part of it is deployed.

Three things make this checkable:

1. **Design choices are written down** before building: each non-obvious choice
   and the rule it serves, and what YAGNI left out.
2. **Tests are written before the code they test.**
3. **An independent review of the full diff against each of the nine rules**,
   by an agent that did not write the code and was not told your conclusions.
   Your own re-read does not count. Every finding is fixed, or listed for the
   owner with the reason. Its result fills the "Design rules" row below.

## What scales with risk

| | Low | Medium | High |
|---|---|---|---|
| Plan | Short; goes in the PR description | Written; one independent review; sent to the owner, then continue | Written; independent reviews of spec fit and of design; **owner approves before building** |
| Independent quality review (above) | Once, on the full diff | Once, on the full diff, plus the `code-review` skill at high effort with `--fix` | Per task and on the full diff by the most capable model, plus `code-review` at high effort with `--fix` |

## Definition of done

Before the push:

- Every pre-push command from the project settings passed.
- **Run it:** if the diff changes code the application executes, the application
  was started, every changed endpoint was called, and the user flow was walked
  in each client that calls it. Test accounts only.
- **Open-PR check:** the diff (not the title or file list) of every open PR
  against the base branch was read for the same files and for anything this
  branch freezes or counts.
- The gate table is complete.

Then push the branch and open the PR against the base branch with the gate
table in the description, read the CI result, and fix failures.

After the owner merges: once the base branch is deployed to the test
environment, repeat the "Run it" flow there and report the result. The tracker
card moves to done only after that passed or did not apply.

## Gate table (in every PR description)

```markdown
| Gate | Result |
|------|--------|
| Risk | <level, why> |
| Plan | <where it is; who reviewed it; findings fixed> |
| Design rules | SOLID / KISS / DRY / YAGNI / fail loudly / least surprise / cost / behaviour / reversibility: <pass, n/a, or finding and fix, per rule> |
| Independent review | <who reviewed what; n found, n fixed, n left for owner> |
| Tests and pre-push checks | <each command: passed, where it ran / not run, why> |
| Run it | <endpoints called, flows walked, in which client, what was seen / no application code> |
| Open-PR check | <PRs read; conflicts or none> |
| Runtime before and after | <numbers / no script, query or hot path changed> |
| Rollback | <how to revert; what breaks if only part is deployed> |
| Not verified | <what, or "nothing"> |
```

## Stop for the owner only when

- The task is High risk: before building, for plan approval.
- A review finding would change scope or intended behaviour. If the finding
  can be handled by doing less than the task asked, do less, say so in the PR,
  and continue; stop only when it needs more or something different.
- An open PR conflicts with this one (give a recommended merge order).
- A check could not run, or a finding is left that you cannot fix.
- Anything destructive or hard to undo.
- Merging, and any push to the base or production branch: always the owner's.

Otherwise keep going until the PR is open and its CI is green.

## New piece of work: spec and cards first

For a refactor or anything spanning several PRs: ask scoping questions one at a
time with a recommendation; audit the latest base branch with evidence (file
and line); write a spec and a card list with a risk level per card; have
independent agents check the spec (facts, card split, behaviour preservation,
consistency); fix the findings. The owner approves the design, the size of the
commitment, and creating the cards.

## Common mistakes

| Thought | Reality |
|---------|---------|
| "Low risk, so no quality review" | Risk scales the rounds, not the bar. Every code PR gets the independent nine-rule review. |
| "I re-read my diff against the rules" | The author cannot be the independent reviewer. |
| "The tests pass, no need to start the app" | Tests check what the author thought of. Running it shows what the user gets. |
| "CI runs the tests anyway" | The project asks for local checks before the push. |
| "I looked at the open PRs' file lists" | A one-line change elsewhere can break a frozen baseline. Read the diffs. |
| "I'll check in with the owner to be safe" | Outside the stop list, checking in is the failure. Keep going and report in the table. |
| "It is merged, the card is done" | Done is after the flow works on the test environment. |

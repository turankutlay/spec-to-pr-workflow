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
review).

A check that did not run is reported as not run, with the reason. It is never
dropped and never replaced by a later check ("CI will catch it").

## Project settings

Read the `## Delivery workflow` section of the project's CLAUDE.md: base
branch, pre-push commands and how to run them when the local runtime is the
wrong version, how to start the application and its clients, test accounts,
test environment, how external services are faked in tests, tracker, and how
the project rates risk. If the section is missing, ask the owner once and offer
to add it (the skill's README has a template).

## Risk

Rate the task before starting, say the rating and why, and use the higher
level when unsure.

| Level | Typical signs |
|-------|---------------|
| Low | Docs, tooling, CI, dead-code removal, mechanical moves fully covered by tests |
| Medium | Changes code the application runs, with tests covering it: a restructure that keeps the contract, or a feature or fix that changes it for clients deployed together with it |
| High | Money, authentication, data migration or deletion, a contract used by clients you do not deploy together (third parties, released mobile apps), or code without test coverage |

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
- **Behaviour preservation.** Where the contract is kept, it is proven kept
  (see "Run it").
- **Reversibility.** The PR says how to roll it back and what breaks if only
  part of it is deployed.

Three things make this checkable:

1. **Design choices are written down** before building: each non-obvious choice
   and the rule it serves, and what YAGNI left out.
2. **Tests are written before the code they test.**
3. **An independent review of the full diff: correctness first, then each of
   the nine rules**, by an agent that did not write the code and was not told
   your conclusions. For correctness it looks for wrong conditions, missed
   cases, behaviour the diff removed, and callers the change breaks. Your own
   re-read does not count. Every finding is fixed, or listed for the owner
   with the reason. Its result fills the "Design rules" row below.

## What scales with risk

| | Low | Medium | High |
|---|---|---|---|
| Plan | Short; goes in the PR description | Written; one independent review against the code; sent to the owner, then continue | Written; independent reviews of spec fit and of design; **owner approves before building** |
| Independent quality review (above) | Once, on the full diff | Once, on the full diff, plus a run of the `code-review` skill at high effort with `--fix`, targeted at this branch against the base branch | Per task and on the full diff by the most capable model, plus the same `code-review` run |

Where the table names the `code-review` skill, invoke it; an inline bug hunt
does not replace it.

## Definition of done

Before the push:

- **Test what changed, not everything.** The quick pre-push commands (style,
  lint, gates) run on every push. For the test suite, run locally the tests
  that cover the changed code: the tests the task names, plus every test file
  that references a changed class, route or script. The full suite is CI's
  job; read its result on the PR before calling the work done. Run the full
  suite locally only when the task is High risk or the diff is cross-cutting
  (base classes, middleware, helpers, config, migrations, dependencies, test
  infrastructure).
- **Tests you add or touch never reach a real external service.** Mail,
  payment, auth, push and storage providers are mocked or faked, and no local
  run may send real mail or charge real money. If you find an existing test
  that does, say so in the PR and propose the fix.
- **Run it.** Decide from the diff, not from the task's label. The contract is
  everything a caller or user can observe:
  - for an HTTP API: status, body shape, headers, redirects;
  - for a library: public signatures, return values, errors;
  - for a CLI: arguments, output, exit code, files written;
  - for a job, event or schedule: its trigger, payload and retries;
  - for a UI: what renders and how it responds;
  - in every case: data written, and mail, push or other messages sent,
    including their text.

  **Contract kept:** tests that pin the contract of each changed entry point
  pass on the base commit and, unchanged, on this branch. Where one is missing,
  write it and run it on the base commit first. No manual run, except for UI
  code that no test renders: run that screen.

  **Contract new or changed:** start the application and exercise each changed
  entry point the way its callers do, in every client that calls it (browser,
  emulator, CLI shell), with test accounts: the main path and the main error
  case. Do not exercise what the diff does not touch.
- **Open-PR check:** the diff (not the title or file list) of every open PR
  against the base branch was read for the same files, and for changes that
  would break a baseline, snapshot or count this branch relies on.
- The gate table is complete.

Then push the branch and open the PR against the base branch with the gate
table in the description, read the CI result, and fix failures. A CI failure
not caused by the code (a runner, download or resource limit) is diagnosed
from the log, fixed or re-run, and named in the PR.

After the owner merges: once the base branch is deployed to the test
environment, repeat the "Run it" flow there for contract changes and report
the result. Move the tracker item past "merged" only after that passed or did
not apply, and no further than the project settings allow. If the session has
ended by then, the PR says what is left to check there.

## Several PRs in a row (stacked)

When the owner asks for a series of PRs to be built one after another and
merged later:

- Each branch starts from the previous one; each PR targets the base branch
  and says, at the top, which PR it stacks on and the merge order. Each PR's
  reviews, "Run it" and gate table cover its own increment (its diff against
  the PR below).
- The open-PR check skips the PRs in your own stack; it still applies to every
  other open PR.
- A fix to a lower PR is committed on its branch and merged up through every
  branch above it, which are then pushed. Never rewrite pushed history.
- When the base branch moves, merge it into the lowest branch and up the
  stack; re-run the pre-push checks and tests at each level, and repeat reviews
  and "Run it" only for a PR whose own code the merge changed.
- Ask the owner to merge in order with merge commits. If the repository only
  allows squash merges, after each merge merge the base branch into the next
  branch and resolve it before the owner merges that one.
- Read every PR's CI, not only the top one.
- In the series, a review finding that changes scope, a finding you cannot fix,
  or a check that could not run does not stop the work: write it into that
  PR's "Independent review" or "Not verified" row and continue. High-risk plan
  approval, anything destructive, and merging still stop. When the series is
  done, list every open decision in one message, with links to the PRs.

## Keep the tracker current

When the work comes from an item in an issue tracker (a Trello card, a GitHub
issue or project item, a Jira or Linear ticket), the item shows where the work
stands, at the moment it changes. Do this without being asked.

| When | Do |
|------|-----|
| You start | Move the item to the "in progress" column or state |
| The PR is open | Move it to the "in review" column and add the PR link as a comment |
| The owner merged it | Move it to the "merged" or "deployed to test" column and add a comment: commit, CI result, test-environment check or why none applies |
| You stop for the owner, or are blocked | Add a comment saying what you are waiting for; leave the column |

- Use the mapping in the project settings; if there is none, match the
  tracker's columns by name, say which you chose, and offer to record it. If
  none matches, leave the item and comment. Never move an item into a column
  for someone else's step (manual testing, release, final sign-off) unless the
  project settings say you may.
- An item with no code to merge (a settings change, a decision) goes to "done"
  when the change is made and verified.
- One PR can cover several items: update every one of them. If the work has no
  tracker item, do not create one unless asked.

## Gate table (in every PR description)

```markdown
| Gate | Result |
|------|--------|
| Risk | <level, why> |
| Plan | <where it is; who reviewed it; findings fixed> |
| Design rules | SOLID / KISS / DRY / YAGNI / fail loudly / least surprise / cost / behaviour / reversibility: <pass, n/a, or finding and fix, per rule> |
| Independent review | <who reviewed what, for correctness and the rules; n found, n fixed, n left for owner> |
| Tests and pre-push checks | <style, lint, gates: passed, where; tests run locally and why those; full suite: CI result, or local for High risk or cross-cutting; external services faked> |
| Run it | <contract kept: the pinning tests, passing on base and branch / contract changed: entry points exercised, in which client, what was seen / no application code> |
| Open-PR check | <PRs read; conflicts or none> |
| Runtime before and after | <numbers / no script, query or hot path changed> |
| Rollback | <how to revert; what breaks if only part is deployed> |
| Not verified | <what, or "nothing"> |
```

## Stop for the owner only when

- Project settings are missing (ask once).
- New piece of work: the scoping questions and approvals under "spec and cards
  first".
- The task is High risk: before building, for plan approval.
- A review finding would change scope or intended behaviour. If the finding
  can be handled by doing less than the task asked, do less, say so in the PR,
  and continue; stop only when it needs more or something different.
- An open PR outside your own stack conflicts with this one (give a
  recommended merge order).
- A check could not run, or a finding is left that you cannot fix.
- Anything destructive or hard to undo.
- Merging, and any push to the base or production branch: always the owner's.

In a stacked series, see that section for which of these do not stop the
work. Otherwise keep going until the PR (or every PR in the series) is open and
its CI is green.

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
| "I re-read my diff against the rules" | The author cannot be the independent reviewer, and an inline bug hunt is not the `code-review` skill. |
| "It is a refactor, so no need to run it" | Only if tests pin the changed entry points' contract on the base commit and on this branch. |
| "To be safe I'll run the whole suite and walk the whole app" | Test what the diff touches. The full suite is CI's job unless the task is High risk or cross-cutting. |
| "I looked at the open PRs' file lists" | A one-line change elsewhere can break a baseline you rely on. Read the diffs. |
| "I'll check in with the owner to be safe" | Outside the stop list, checking in is the failure. Keep going and report in the table. |

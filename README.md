# spec-to-pr-workflow

A Claude Code skill that turns a task or tracker card into a pull request that
is tested, reviewed for quality and ready to merge, with the evidence in one
table in the PR description. Claude works without check-ins and stops for you
only at fixed points: missing project settings, the approvals for a new
multi-PR piece of work, High-risk plan approval, a change of scope, a conflict
with another open PR, a check that could not run or a finding it cannot fix,
anything destructive, and merging, which is always yours. In a series of
stacked PRs, findings are written into the PRs and the work continues; you get
one list of open decisions at the end.

Refactors are proven by tests that pin the contract before and after; new or
changed behaviour is also run in the real client (browser, emulator, CLI).

## What it costs

Agent runs per PR:

- **Low risk:** the implementation, plus one independent review agent.
- **Medium risk:** the above, plus an independent plan review and a run of the
  `code-review` skill (which starts its own reviewers).
- **High risk:** reviews per task and on the full diff by the most capable
  model, and your approval of the plan before building.

So a PR costs several times the tokens of a plain "do this" request. In return
the reviews find what the implementation missed: in the run that shaped this
skill, almost every one of 13 PRs had at least one real finding (a leaked
payment secret in logs, a query that did nothing, tests calling a live mail
API).

## Install on a computer

Clone this repository into the personal skills folder:

```bash
git clone https://github.com/turankutlay/spec-to-pr-workflow.git ~/.claude/skills/spec-to-pr-workflow
```

Update later with `git -C ~/.claude/skills/spec-to-pr-workflow pull`.

It needs a Claude Code version that ships the built-in `code-review` skill
(check that `/code-review` exists); without it, Medium and High PRs report that
check as not run. Planning and subagent skills (for example the superpowers
plugin) help but are not required.

## Use in a project

Add a `## Delivery workflow` section to the project's CLAUDE.md. Without it,
Claude asks for these once. Template:

```markdown
## Delivery workflow

- **Base branch:** <base branch>. <production branch and how it is updated>.
- **Branch names:** <branch naming rule>.
- **Risk:** <areas that are always High here, e.g. payments, auth; anything the project rates differently>.
- **Pre-push commands:** <style, lint, static analysis, gates, the tests for the change>.
- **Wrong local runtime:** <how to run the commands in Docker or another runtime>.
- **Run locally:** <how to start the API and each client; test accounts and where their passwords live>.
- **Test environment:** <URL; how it is deployed from the base branch>.
- **External services in tests:** <how mail, payments, auth and push are faked>.
- **Tracker:** <board>; columns: <in progress> → <in review> → <merged / deployed to test>. Columns after that belong to <who>.
- **Specs and plans:** <folder>.
- **Merging:** <who merges; merge commits or squash>.
```

Then ask Claude to work a card or task; the skill triggers on that.

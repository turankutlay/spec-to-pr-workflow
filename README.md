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

## Requirements

- Claude Code with the built-in `code-review` skill (type `/code-review` to
  check). Without it, Medium and High PRs report that check as not run.
- `git` and the GitHub CLI (`gh`), signed in, so Claude can push branches and
  open PRs.
- To keep a tracker current, a connection to it (for example the Trello, Jira,
  Linear or GitHub MCP connector). Without one, the work still runs; the
  tracker steps are reported as not done.
- Optional: planning and subagent skills such as the superpowers plugin.

## Install

**For yourself, on every project.** Clone into your personal skills folder
(Git Bash, macOS or Linux; in PowerShell `~` works the same):

```bash
git clone https://github.com/turankutlay/spec-to-pr-workflow.git ~/.claude/skills/spec-to-pr-workflow
```

Update later with `git -C ~/.claude/skills/spec-to-pr-workflow pull`.

**For a whole team, in one repository.** Copy the folder to
`.claude/skills/spec-to-pr-workflow/` in the repository and commit it. Everyone
who opens Claude Code in that repository gets the same version, and changes go
through review like any other file.

Check it loaded: start a new Claude Code session and type `/` — the list
should show `spec-to-pr-workflow`.

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

## Ask for work

The skill starts when you hand Claude a task, a card or a plan. Examples:

- "Work Trello card [S2-03]." — one PR, built, reviewed, tested and opened.
- "Fix the login redirect bug in issue #212 and open a PR." — the same for a
  bug; because it changes behaviour, Claude also runs the flow in the browser.
- "Implement cards S1-01 to S1-12 one after another; we will merge them later."
  — a stack of PRs, each built on the previous one, with one list of open
  decisions at the end.
- "We want to refactor the payments module. Write a spec and cards first." —
  scoping questions, an audit with file and line evidence, a reviewed spec,
  and cards for your approval.

You can also type `/spec-to-pr-workflow` followed by the task.

What you get back: a PR against the base branch whose description has the
gate table (risk, plan, design rules, reviews, tests, how it was run, open-PR
check, cost, rollback, what was not verified), CI read and fixed, the tracker
item moved, and a short message naming anything that needs your decision.
Claude never merges.

## Licence

MIT; see [LICENSE](LICENSE).

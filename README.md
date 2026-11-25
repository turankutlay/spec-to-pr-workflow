# spec-to-pr-workflow

Two Claude Code skills that take a piece of work from an idea to merged code:

| Skill | From | To |
|---|---|---|
| [`spec-and-cards`](skills/spec-and-cards/SKILL.md) | an idea or a one-paragraph brief | an approved spec and checked tracker cards, each with risk, size and a testable "done when" |
| [`spec-to-pr-workflow`](skills/spec-to-pr-workflow/SKILL.md) | one card, task or plan | a pull request that is tested, reviewed and ready to merge, with the evidence in one table |

Use them together for anything that spans several PRs, or the second one on
its own for a single task. Claude never merges; the decisions stay yours.

## spec-and-cards

Turns an idea into an approved spec and tracker cards that a builder can pick
up without guessing. Nothing is built. Claude:

1. looks for existing work and for other people's work in the same area;
2. frames the problem with you first: the customer and their pain, the
   evidence, target and guardrail metrics with a date, and the alternatives
   (always including doing nothing and the cheapest option), offered as a
   draft for you to correct;
3. records the base commit of every repo and audits the code, with file and
   line evidence, so the design questions rest on facts;
4. asks you design questions **one at a time**, each with options, a
   recommendation **and its reason**, what would change it, and whether the
   choice can be undone later; it works through a checklist of areas (roles,
   what each role sees, security and privacy, lifecycle, money, defaults,
   concurrency, performance, notifications, old clients, launch, legal,
   reporting, testing setup, sign-off, other people's work);
5. sketches the key screens for you to react to;
6. writes the spec in a place your team can open, with a **launch plan**
   (stages, guardrail metrics, monitoring and alerts in place before the
   first stage, runbook, clean-up) and a **sign-off list** of who must
   approve what;
7. has up to six independent reviewers check it (facts, card split, money
   and behaviour preservation, consistency, fit with the existing app,
   security and privacy), scaled to risk, and repeats under a stop rule;
8. gets your approval, and for High risk the approval of everyone on the
   sign-off list; agent reviews come before human sign-off, not instead of it;
9. writes **decision records (ADRs)** for the choices that shape the
   architecture, data model, public contract or money rules;
10. creates the cards (overview, build cards with risk, size and a testable
    "done when", and when needed a clean-up card and a dated post-launch
    review card), reads them back, and leaves a hand-over.

A worked example of the output is in
[skills/spec-and-cards/example.md](skills/spec-and-cards/example.md).

Other people's cards are never made to wait for yours without your explicit
say-so, and questions for colleagues go on question cards addressed to them.

**What it costs:** a High-risk project (money, several repos, released apps)
runs about five read-only audit agents and six reviewers per round, usually
two or three rounds. A small feature needs one audit agent and three
reviewers, and a one-line launch plan. In the run
that shaped this skill, every round found real problems before any code
existed (a possible deadlock, data leaking to the wrong users, a payment path
nobody had listed, a dependency that would have blocked a colleague).

**Ask for it:**

- "We want a referral programme. Scope it and make Trello cards. Build
  nothing yet."
- "Recheck the cards for the payments refactor against the base branch."

Or type `/spec-to-pr-workflow:spec-and-cards` followed by the idea.

## spec-to-pr-workflow

Turns a task or tracker card into a pull request that is tested, reviewed for
quality and ready to merge, with the evidence in one table in the PR
description. Claude works without check-ins and stops for you only at fixed
points: missing project settings, High-risk plan approval, a change of scope,
a conflict with another open PR, a check that could not run or a finding it
cannot fix, anything destructive, and merging, which is always yours. In a
series of stacked PRs, findings are written into the PRs and the work
continues; you get one list of open decisions at the end.

Refactors are proven by tests that pin the contract before and after; new or
changed behaviour is also run in the real client (browser, emulator, CLI).

**What it costs** (agent runs per PR):

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

**Ask for it:**

- "Work Trello card #42." — one PR, built, reviewed, tested and opened.
- "Fix the login redirect bug in issue #212 and open a PR." — the same for a
  bug; because it changes behaviour, Claude also runs the flow in the browser.
- "Implement cards #40 to #45 one after another; we will merge them later."
  — a stack of PRs, each built on the previous one, with one list of open
  decisions at the end.

What you get back: a PR against the base branch whose description has the
gate table (risk, plan, design rules, reviews, tests, how it was run, open-PR
check, cost, rollback, what was not verified), CI read and fixed, the tracker
item moved, and a short message naming anything that needs your decision.

Or type `/spec-to-pr-workflow:spec-to-pr-workflow` followed by the task.

## Requirements

- Claude Code with subagents (the Agent tool) and, if your version has it,
  the built-in `code-review` skill (type `/code-review` to check). Without
  `code-review`, Medium and High PRs report that check as not run; without
  subagents, `spec-and-cards` runs the audit and reviews itself, one aspect
  at a time.
- `git` and the GitHub CLI (`gh`), signed in, so Claude can read the base
  branch, push branches and open PRs.
- To create and update cards, a connection to your tracker (for example the
  Trello, Jira, Linear or GitHub MCP connector). Without one, `spec-and-cards`
  writes the card texts for you to paste, and `spec-to-pr-workflow` reports
  the tracker steps as not done.
- Optional: planning and subagent skills such as the superpowers plugin.

## What it does without asking

Within the work you hand over, Claude reads your repositories and tracker,
creates branches, commits, pushes and opens pull requests, comments on and
moves tracker cards, and creates cards once you approve them. It stops for
you before merging, before anything destructive, and at the decision points
listed above. Your Claude Code permission settings still apply to every step.

## Install

**For yourself, on every project.** In Claude Code:

```text
/plugin marketplace add turankutlay/spec-to-pr-workflow
/plugin install spec-to-pr-workflow@turankutlay
```

Update later with `claude plugin update spec-to-pr-workflow@turankutlay`, or
from the Installed tab in `/plugin`. (`/plugin marketplace update turankutlay`
only refreshes the list of available versions.)

**For a whole team, in one repository.** Add this to the repository's
`.claude/settings.json` and commit it. It turns the plugin on for the
repository but does not download it:

```json
{
  "extraKnownMarketplaces": {
    "turankutlay": {
      "source": { "source": "github", "repo": "turankutlay/spec-to-pr-workflow" }
    }
  },
  "enabledPlugins": {
    "spec-to-pr-workflow@turankutlay": true
  }
}
```

Each teammate then runs this once per clone (Claude Code reports "enabled
in project settings but not installed" until they do):

```bash
claude plugin install spec-to-pr-workflow@turankutlay --scope project
```

**Without the plugin system.** Copy the two folders in `skills/` to
`~/.claude/skills/` (for yourself) or to `.claude/skills/` in a repository
(for a team):

```bash
git clone https://github.com/turankutlay/spec-to-pr-workflow.git /tmp/spec-to-pr
cp -r /tmp/spec-to-pr/skills/* ~/.claude/skills/
```

Check it loaded: start a new Claude Code session and type `/`. The list shows
both skills (with the plugin they appear as
`spec-to-pr-workflow:spec-and-cards` and
`spec-to-pr-workflow:spec-to-pr-workflow`).

**Upgrading from version 1** (the repository cloned into
`~/.claude/skills/spec-to-pr-workflow`): a `git pull` there leaves a nested
`skills/` folder that Claude Code does not load, so the skill silently stops
working. Delete the folder instead, then install the plugin as above:

```bash
rm -rf ~/.claude/skills/spec-to-pr-workflow
```

In PowerShell: `Remove-Item -Recurse -Force ~/.claude/skills/spec-to-pr-workflow`.

## Use in a project

Add a `## Delivery workflow` section to the project's CLAUDE.md. Both skills
read it; without it, Claude asks for these once. The template is in
[skills/spec-to-pr-workflow/settings-template.md](skills/spec-to-pr-workflow/settings-template.md).

## Releasing a new version

Bump `version` in `.claude-plugin/plugin.json` (the only place it is set);
installed copies only update when it changes.

## Licence

MIT; see [LICENSE](LICENSE).

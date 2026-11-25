---
name: spec-and-cards
description: Use when asked to scope, plan or make tracker cards for a feature or multi-PR project before code is written, especially one touching money, several repos or clients, released apps, or work another team owns; also when an approved spec and its cards need rechecking because the code or decisions moved
---

# Spec and cards

## Overview

Turn an owner's idea into an approved spec and a set of tracker cards that a
builder can pick up without guessing. Nothing is built. "Owner" here means
the person who asked for the work and makes the decisions.

**Core principle:** start from the problem and how success is measured; every
decision is the owner's, made one question at a time with a reasoned
recommendation; every fact comes from the current base branch with file:line
evidence; every card is checked by reviewers who did not write it, and High
risk work is also read and signed off by people.

Building each card into a PR is the `spec-to-pr-workflow` skill's job. A short
worked example of the output is in `example.md`.

**Not for small work.** If the whole change fits in one small PR and touches
no money, authentication, personal data, migration, public contract or
released client, skip this skill: write one card (or hand the task straight
to `spec-to-pr-workflow`).

## Steps

Create one task per step and keep the list current.

1. **Read the project settings** (the `## Delivery workflow` section of the
   project's CLAUDE.md: tracker, base branch, risk rules, where specs and
   decision records live, who signs off). If there is none, ask once.
2. **Find existing work.** Search the tracker, specs, decision records,
   project memory or notes (if any) and open PRs for the topic and its
   neighbours (same tables, same screens, same money paths). Note every
   person or team that owns nearby work.
3. **Frame the problem** with the owner before any design question. Do not
   ask open questions: offer a **draft** for the owner to correct, one part
   per question in the question format (below): first the customer and their
   problem with the evidence you found, then the target and guardrail
   metrics with a date (proposed numbers, marked as guesses), then the
   alternatives (always including doing nothing and the cheapest option) and
   what is out of scope. For a refactor, the "customer" is whoever is hurt
   today, and guardrails ("nothing users see changes") carry most of the
   weight. If the owner cannot state a measurable goal, record that as a
   risk; never invent one.
4. **Record the base.** Fetch the base branch of every repo involved and write
   down each commit. All reading is from that branch (`git show
   origin/<base>:<path>`), never a local checkout.
5. **Quick audit before design questions.** One read-only agent per repo or
   area, in parallel, each returning file:line facts: current behaviour, every
   place the topic is computed, shown or stored, the clients that call it,
   what released apps do, and current load or volumes where cost matters.
6. **Ask design questions one at a time** (format below), working through the
   question areas (below). Record each answer as a numbered decision (Q1, Q2
   …) with the date and the owner's own words.
7. **Sketch the key screens** for anything a user or staff member will see,
   and let the owner react before the spec is final.
8. **Write the spec** from the template in `templates.md`, in a place the
   team can open (a shared doc or page, or `docs/` in the repo once approved),
   never only a local file. Fill the sign-off list (below) and the launch
   plan.
9. **Review with independent agents**, one aspect each, in parallel (briefs
   in `reviewer-briefs.md`), scaled to risk (below). Give every reviewer the
   same exported copy of the spec and cards, and the code; never your
   conclusions.
10. **Fix and repeat** under the stop rule (below). A finding that changes
    behaviour, money, scope, security or someone else's work becomes a
    question for the owner, with a recommendation. Everything else is fixed
    directly. Bump the spec revision and add a line to its history each time.
11. **Sign-off.** The owner approves the spec, the cards with risk and size,
    and in plain words **every dependency on or from other people's work**.
    For High risk, every person on the sign-off list reads the final spec and
    approves their part. Agent reviews come before human sign-off, never
    instead of it. Create nothing before these yeses.
12. **Record lasting decisions** as decision records (ADRs, template in
    `templates.md`) in the repo's decision folder, for every choice that
    shapes the architecture, data model, public contract or money rules. The
    first build card commits them if the repo is not yours to write to yet.
13. **Create the cards** from the card template: description complete on its
    own, label, repo tag, feature tag. Always include an overview card. Add a
    clean-up card (with a due date) whenever the work leaves a flag,
    deprecated field or temporary path behind, and a post-launch review card
    (dated) whenever the spec sets a target metric. Questions for other
    people go on question cards addressed to them.
14. **Read back** every created card (export them to one file) and check it
    against the spec.
15. **Hand over.** Save decisions, card ids, base commits and open questions
    where the next session will find them (project memory, a notes file, or
    the overview card).

**Before building starts** (often days later): re-audit the base branch, update
the spec to what changed, and recheck the cards. Code moves.

## Without a tool

| Missing | Do instead |
|---|---|
| Subagents | Run the audit and each review yourself, one aspect at a time, ideally each in a fresh session given only the spec, the cards and the code |
| Tracker connector | Write the card texts to a file for the owner to paste; the read-back compares the pasted cards with that file |
| Shared doc or page | Commit the spec draft to a branch of the repo and share the link |
| Screen mocks | Describe each screen as a short list of what it shows and does |

## Question format

Each question shows, in this order:

1. The decision in one sentence, with the fact that makes it necessary.
2. Two to four options, each with its consequence for users, money and effort.
3. **Recommendation and why**, tied to a fact or rule ("admins only, because
   discounts change revenue").
4. **What would change the recommendation** ("if customers need
   self-service").
5. **Whether it can be changed later.** Hard-to-undo choices first.

Leave room for the owner's own answer, and record their words. If a question
tool returns no answer, ask again in plain text; never assume one.

## Question areas

Cover every area before the spec is approved; mark the ones that do not apply.

| Area | Ask about |
|---|---|
| Problem and success | customer problem, evidence, target and guardrail metrics, alternatives, out of scope |
| Actors and permissions | who creates, edits, uses; team members; staff roles; admins acting as a user |
| What each role sees | each user type, team members, the other party in a deal, staff, the public, released apps |
| Security and privacy | personal or payment data, who can read or change what, impersonation, abuse and fraud, retention |
| Lifecycle | draft, approval, edit, duplicate, cancel, delete, expiry, account purge |
| Money | charge, rounding, refunds, cancellations, payouts, tax, invoices, mixed carts |
| Defaults and limits | defaults on forms, usage limits, time zones, stacking |
| Concurrency | two users at once, last unit, settle vs change |
| Performance and cost | expected volume, query cost, rate limits, third-party costs |
| Notifications | emails, push, in-app, when and to whom |
| Old clients | released app versions, API contract, forced update |
| Launch | staged rollout, guardrails, monitoring and alerts, runbook, kill switch, rollback, clean-up |
| Legal and copy | terms, FAQs, legal wording, help texts |
| Reporting | what staff and finance need to see |
| Testing setup | test accounts, seed data, how to switch the feature on in dev, which environments |
| Sign-off | who besides the owner must approve: security, privacy, legal, finance, design, other teams |
| Other people's work | open PRs, other teams' cards, who decides on them |

## Reviewers by risk

| Risk | Reviewers (briefs in `reviewer-briefs.md`) | Human sign-off |
|---|---|---|
| High (money, auth, personal data, migrations, released clients, several repos) | all six: facts, card split, money and behaviour, consistency, fit with the app, security and privacy | owner plus everyone on the sign-off list |
| Medium | facts, consistency, fit with the app; security and privacy if personal data or permissions change | owner |
| Low | facts | owner |

## Stop rule for reviews

- A round finds something that changes behaviour, money, security or scope:
  fix it and run a full round again.
- A round finds only wording or small gaps: fix them and check only those
  fixes with one targeted reviewer.
- A full round finds nothing that changes behaviour, money or security: stop.

## Launch plan

For High risk, or anything touching money, personal data, a public contract
or released apps, the full plan below. For other Low or Medium work, one
line is enough: "ships directly; watch <error tracker> for a day; rollback =
revert the PR"; add a flag only if a revert is not enough.

- **Stages:** internal or test accounts, then a small share of real users,
  then everyone; what moves it to the next stage and who decides.
- **Guardrails:** the metrics that stop or roll back a stage, with
  thresholds (errors, payment failures, refunds, support tickets).
- **Error budget:** the failure rate the feature may have (for example
  "payment failures under 0.5% a day" or "99.9% of quotes succeed");
  spending it pauses the rollout until the cause is fixed.
- **Monitoring:** the dashboards, logs and alerts that exist before stage
  one, who receives the alerts, and for each money or other critical path
  the alert that fires if it goes wrong out of hours. Each is built by a
  named card.
- **Runbook:** how to switch it off, how to roll back, who to call.
- **Clean-up:** what to remove after full rollout, and when (a card).
- **Review:** the post-launch review date and the metrics it checks (a card).

## Other people's work

- Your cards may wait for theirs; theirs never wait for yours unless the
  owner said so in plain words.
- A reviewer's suggestion to couple your work with theirs is not a decision:
  ask the owner first.
- Tell them through their own card (a short factual comment, "your call") or a
  question card, only with the owner's approval, and never put a dependency
  on their card without it.
- A bug found in their code is reported to them; it does not become your
  card's hidden scope.

## Common mistakes

| Mistake | Instead |
|---|---|
| Starting from the solution | Frame the problem, metrics and alternatives first |
| Asking all questions at once, without recommendations | One at a time, reasoned recommendation, what would change it |
| Design questions before the audit | Quick audit first; options grounded in facts |
| Spec on a base that moved | Record base commits; re-audit before build |
| Late decisions (visibility, defaults, emails, legal) | Work through the question areas up front |
| "Fit with the app" or a data leak found after cards exist | Run those reviewers in the first round |
| Agent reviews treated as sign-off | People on the sign-off list approve High risk |
| On/off switch as the whole launch plan | Stages, guardrails, monitoring, runbook |
| Flags and deprecated fields left forever | Clean-up card with a due date |
| Decisions only in the tracker | Decision records in the repo |
| Reviewers reading different card versions | Export once; every reviewer gets the same file |
| Scope added as card comments | Update the description; comments for history only |
| Endless review rounds | Stop rule |
| Cards without risk, size or done-when | Card template fields are required |
| Making another team's card wait for yours | Ask the owner; tell them on their card |

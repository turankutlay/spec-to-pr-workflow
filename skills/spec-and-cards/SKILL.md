---
name: spec-and-cards
description: Use when asked to scope, plan or make tracker cards for a feature or multi-PR project before code is written, especially one touching money, several repos or clients, released apps, or work another team owns; also when an approved spec and its cards need rechecking because the code or decisions moved
---

# Spec and cards

## Overview

Turn an owner's idea into an approved spec and a set of tracker cards that a
builder can pick up without guessing. Nothing is built. "Owner" here means
the person who asked for the work and makes the decisions.

**Core principle:** every decision is the owner's, made one question at a time
with a reasoned recommendation; every fact comes from the current base branch
with file:line evidence; every card is checked by reviewers who did not write
it.

Building each card into a PR is the `spec-to-pr-workflow` skill's job.

## Steps

Create one task per step and keep the list current.

1. **Read the project settings** (the `## Delivery workflow` section of the
   project's CLAUDE.md: tracker, base branch, risk rules, where specs live).
   If there is none, ask once. Then **find existing work.** Search the tracker, specs, project memory or notes (if any) and open PRs for
   the topic and its neighbours (same tables, same screens, same money paths).
   Note every person or team that owns nearby work.
2. **Record the base.** Fetch the base branch of every repo involved and write
   down each commit. All reading is from that branch (`git show
   origin/<base>:<path>`), never a local checkout.
3. **Quick audit before questions.** One read-only agent per repo or area,
   run in parallel, each returning file:line facts: current behaviour, every
   place the topic is computed, shown or stored, the clients that call it, and
   what released apps do. Questions come after, grounded in these facts.
4. **Ask scoping questions one at a time** (format below), working through
   the question areas (below). Record each answer as a numbered decision
   (Q1, Q2 …) with the date and the owner's own words.
5. **Sketch the key screens** (a simple mock or artifact) for anything a user
   or staff member will see, and let the owner react before the spec is final.
6. **Write the spec** from the template in `templates.md`, in a place the
   team can open (a shared doc or page, or `docs/` in the repo once approved),
   never only a local file.
7. **Review with independent agents**, one aspect each, in parallel (briefs
   in `reviewer-briefs.md`): facts, card split and delivery, money and
   behaviour preservation, consistency, and fit with the existing app. Give
   every reviewer the same exported copy of the spec and cards, and the code;
   never your conclusions.
8. **Fix and repeat** under the stop rule (below). A finding that changes
   behaviour, money, scope or someone else's work becomes a question for the
   owner, with a recommendation. Everything else is fixed directly. Bump the
   spec revision and add a line to its revision history each time.
9. **Owner approval.** Show the spec, the card list, each card's risk and size,
   and in plain words **every dependency on or from other people's work**.
   Create nothing before an explicit yes.
10. **Create the cards** from the card template: description complete on its
    own, label, repo tag, feature tag. Add an overview card linking the spec,
    every card and the order. Questions for other people go on question
    cards assigned to (or mentioning) them.
11. **Read back** every created card (export them to one file) and check it
    against the spec.
12. **Hand over.** Save decisions, card ids, the base commits and open
    questions where the next session will find them (project memory, a notes
    file, or the overview card).

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
| Actors and permissions | who creates, edits, uses; team members; staff roles; admins acting as a user |
| What each role sees | each user type, team members, the other party in a deal, staff, the public, released apps |
| Lifecycle | draft, approval, edit, duplicate, cancel, delete, expiry, account purge |
| Money | charge, rounding, refunds, cancellations, payouts, tax, invoices, mixed carts |
| Defaults and limits | defaults on forms, usage limits, time zones, stacking |
| Concurrency | two users at once, last unit, settle vs change |
| Notifications | emails, push, in-app, when and to whom |
| Old clients | released app versions, API contract, forced update |
| Rollout | kill switch, dormant ship, launch steps, rollback |
| Legal and copy | terms, FAQs, legal wording, help texts |
| Reporting and success | what staff and finance need to see; how we know it works after launch (monitoring, logs) |
| Testing setup | test accounts, seed data, how to switch the feature on in dev, which environments |
| Other people's work | open PRs, other teams' cards, who decides on them |

## Stop rule for reviews

- A round finds something that changes behaviour, money or scope: fix it and
  run a full round again.
- A round finds only wording or small gaps: fix them and check only those
  fixes with one targeted reviewer.
- A full round finds nothing that changes behaviour or money: stop.

Scale the reviewers to the risk: all five for High risk (money, auth,
migrations, released clients, several repos); facts plus consistency for a
small Medium feature.

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
| Asking all questions at once, without recommendations | One at a time, reasoned recommendation, what would change it |
| Questions before the audit | Quick audit first; options grounded in facts |
| Spec on a base that moved | Record base commits; re-audit before build |
| Late decisions (visibility, defaults, emails, legal) | Work through the question areas up front |
| "Fit with the app" found after cards exist | Run it as a reviewer in the first round |
| Reviewers reading different card versions | Export once; every reviewer gets the same file |
| Scope added as card comments | Update the description; comments for history only |
| Endless review rounds | Stop rule |
| Spec only on a local disk | Shared doc or repo `docs/` |
| Cards without risk, size or done-when | Card template fields are required |
| Making another team's card wait for yours | Ask the owner; tell them on their card |

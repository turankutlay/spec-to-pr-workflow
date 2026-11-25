# Templates

## Spec

```markdown
# <Feature>: <one-line goal> (spec, revision N)

Status: draft | approved <date>. Nothing is built.
Base: <repo> <commit> for every repo involved, read on <date>.
Start rule: <what must be finished before any card starts, if anything>.

## 1. Problem and success
- Customer and problem: <who, what hurts today>.
- Evidence: <data, requests, incidents; or "none yet" as a stated risk>.
- Target metrics: <metric, current value, target, by when>.
- Guardrail metrics: <what must not get worse, and the threshold>.
- Out of scope.
- Example with real numbers.

## 2. Alternatives considered
| Option | For | Against | Decision |
Include "do nothing" and the cheapest option.

## 3. Owner decisions
| # | Question | Decision (owner's words, date) |

## 4. Facts found
Per repo: current behaviour with file:line; every place the topic is
computed, shown or stored; what released clients do; current volumes;
other people's open work in the same area.

## 5. Design
5.1 Terms (define every word a builder could read two ways)
5.2 Data
5.3 Rules and maths
5.4 Security and privacy (who can read or change what, data that crosses a
    boundary, abuse cases)
5.x One subsection per card group, naming the card

## 6. Behaviour preservation for existing users
What stays identical while the feature is off, and every deliberate change
that applies anyway, each with its test.

## 7. Launch plan
Stages and who moves them; guardrails with thresholds; monitoring and alerts
(in place before stage one, and who receives them); runbook; clean-up; review
date.

## 8. Rollback
Switch-off, code revert window, partial-deploy states across repos.

## 9. Sign-off
| Area | Person | Needed for | Status |
(owner always; security, privacy, legal, finance, design, other teams as
the work requires)

## 10. Decisions still open
Each with a recommendation.

## 11. Cards
| Card | Repo | Content | Risk | Size | Depends on |
Always: an overview card, a clean-up card, a post-launch review card.
Order line, and the release order across repos and stores.

## 12. Notes on other people's cards
What was posted where, with the owner's approval.

## 13. Decision records
The ADRs written for this spec, with their paths.

## 14. Revision history
```

## Card

```markdown
**Part of the <FEATURE> feature** (overview: <link>). Spec sections <n>,
decisions <Qn>. Start rule: <rule>.

**Risk: <Low | Medium | High>** (<why>). **Size: <S | M | L>**.

## Scope (<repo>)
- Exact files, methods, fields, endpoints, error codes.

## Done when
- Testable items: which tests, which manual check, in which environment.
- "Full suite green" where the project requires it.

## Depends on
Cards and other people's work, by link.
```

Card name: `[<Repo>] <FEATURE> <id>: <outcome>`. Apply the feature's label.

## Clean-up card

```markdown
**Clean-up for the <FEATURE> feature** (overview: <link>). Due: <date, e.g.
four weeks after full rollout>.

Remove: <feature flags, deprecated fields (after the deprecation check),
temporary code paths, old texts>. Done when each is gone and tests pass.
```

## Post-launch review card

```markdown
**Post-launch review of <FEATURE>** (overview: <link>). Date: <e.g. 30 and
60 days after full rollout>.

Compare each target and guardrail metric in spec section 1 with its value
now; list incidents, support tickets and surprises; decide keep, change or
remove; record the decision and any follow-up cards.
```

## Decision record (ADR)

```markdown
# <number>. <decision in a few words>

Date: <date>. Status: accepted | superseded by <number>.
Spec: <link>, decision <Qn>.

## Context
The forces and facts that made a decision necessary.

## Decision
What we chose, in one paragraph.

## Alternatives
What else was considered and why it lost.

## Consequences
What becomes easier, what becomes harder, what must now always hold.
```

## Question card (for someone outside the feature)

```markdown
**Question for <person>:** <one sentence>.

## What we found (<repo> <base>, <date>)
Facts with file paths.

## Please answer
Numbered questions, each with the options.

## After the answer
What will be changed, and what waits on it.
```

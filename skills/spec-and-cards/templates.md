# Templates

## Spec

```markdown
# <Feature>: <one-line goal> (spec, revision N)

Status: draft | approved <date>. Nothing is built.
Base: <repo> <commit> for every repo involved, read on <date>.
Start rule: <what must be finished before any card starts, if anything>.

## 1. Goal
What changes for whom; one example with real numbers.

## 2. Owner decisions
| # | Question | Decision (owner's words, date) |

## 3. Facts found
Per repo: current behaviour with file:line; every place the topic is
computed, shown or stored; what released clients do; other people's open
work in the same area.

## 4. Design
4.1 Terms (define every word a builder could read two ways)
4.2 Data
4.3 Rules and maths
4.x One subsection per card group, naming the card

## 5. Behaviour preservation for existing users
What stays identical while the feature is off, and every deliberate change
that applies anyway, each with its test.

## 6. Left out (YAGNI)

## 7. Rollback
Switch-off, code revert window, partial-deploy states across repos.

## 8. Decisions still open
Each with a recommendation.

## 9. Cards
| Card | Repo | Content | Risk | Size | Depends on |
Order line, and the release order across repos and stores.

## 10. Notes on other people's cards
What was posted where, with the owner's approval.

## 11. Revision history
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

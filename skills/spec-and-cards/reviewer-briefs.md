# Reviewer briefs

Each reviewer is a separate agent, read-only, given: the path of the spec, the
path of the exported cards, the repos and their base branch, and how to read
it (`git -C <repo> fetch -q origin <base>`, then `git show origin/<base>:<path>`
and `git grep -n <pattern> origin/<base> -- <paths>`). Never give a reviewer
your own conclusions. Ask for findings ranked worst first, each with evidence
and a concrete fix to the spec or card text, under about 800 words.

## 1. Facts
Check every file, method, field, route, setting and line number the spec and
cards name against the current base: CONFIRMED, MOVED, WRONG or GONE. List new
code since the spec's base that changes the design: other places that compute,
show, store or refund the same thing; new payment, settlement or write paths;
other writers of the same tables (for example an admin app that writes
the database directly).

## 2. Card split and delivery
Is each card one reviewable PR in one repo, with a testable done-when? Are
dependencies complete, with no cycle, and is each card safe if merged and
deployed alone? Are risk levels right? Are release steps across repos and
stores complete (for example: backend before the apps that use it, API
version gates, a forced mobile update)? Does anything make other people's work wait? Missing cards?

## 3. Money and behaviour preservation
For each card merged on its own, and for the feature switched off and then on:
can any existing user pay, be refunded, be paid out, see or be emailed
anything different? Check when a price or rate is fixed, locks and lock order,
rounding and allocation, refunds, payout records, tax amounts, invoices, kill
switch, legacy payment paths, concurrency on counters, account purge, direct
writes by other apps. Is each rule owned by exactly one card, with a test?

## 4. Consistency
Do the owner decisions, design, preservation, rollback, left-out list and
cards agree? Undefined terms, rules stated two ways, and edge cases the
decisions imply but the design does not answer.

## 5. Fit with the existing app
How the feature fits each client's existing flow and components: where it
goes on each screen, loading, empty and error states, shared create/edit
forms, legacy pages, duplicate flows, team members and admins acting as a user, cache
refresh, every text or page that explains the topic (help, FAQ, terms,
emails), translations, staff screens and reports, audit logs, permissions
conventions, API docs, privacy of who sees what, test patterns to extend.

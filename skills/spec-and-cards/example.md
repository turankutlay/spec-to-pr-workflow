# Worked example (shortened)

A fictional booking marketplace: customers book service providers; the
platform charges a 10% booking fee. The owner asks: "Give customers credit
when a friend they referred makes a first booking. Scope it and make cards."

## A question as it is asked

> **Q3. When does the referrer get the credit?** The audit shows bookings can
> be cancelled up to 48 hours before the appointment, with a full refund
> (`BookingService.php:212`).
>
> - **A. When the friend books.** Fastest reward, but a booked-then-cancelled
>   booking pays out credit for nothing; abuse is cheap.
> - **B. When the friend's first appointment has taken place.** Slower, but
>   credit is only paid for real revenue.
> - **C. After the refund window closes.** Same as B for most bookings, one
>   more scheduled job.
>
> **Recommendation: B**, because it ties the cost to revenue we keep and
> closes the cancel-and-rebook abuse path with no new job.
> **What would change it:** if marketing needs an instant reward to make the
> campaign work, A with a cancellation clawback.
> **Can it change later?** Yes for new referrals; credit already paid stays.

## Spec extracts

**1. Problem and success**
- Customer and problem: new customers mostly arrive through paid ads at
  about 40 per booking; word of mouth is not rewarded.
- Evidence: 30% of new customers say a friend recommended us (signup
  survey, last quarter).
- Target: 15% of first bookings come through a referral within 90 days of
  launch (today: not measured, assumed 0).
- Guardrails: referral credit cost stays under 25% of the booking fee
  earned from referred customers; self-referral rate under 1%.

**2. Alternatives considered**

| Option | For | Against | Decision |
|---|---|---|---|
| Do nothing | no cost | ad cost per customer stays high | rejected |
| Discount code for the friend only | simplest | rewards nobody for spreading it | rejected |
| Credit for both, after first appointment | rewards real revenue | needs a ledger entry and a job hook | **chosen** |
| Cash payout | strongest incentive | payment licensing, tax reporting | rejected |

**3. Owner decisions** (extract)

| # | Question | Decision |
|---|---|---|
| Q1 | Who gets credit | Both: referrer 10, friend 10 on their first booking ("keep it symmetric", 2026-03-02) |
| Q3 | When | After the first appointment took place (2026-03-02) |
| Q6 | Limits | 20 referrals a year per customer; one per device and payment card (2026-03-03) |

**5.4 Security and privacy:** the referral link carries a random token, not a
customer id; the friend never sees the referrer's email; credit is refused
when referrer and friend share a payment card or device.

**7. Launch plan**
- Stages: staff accounts (1 week), then 10% of customers, then all.
- Guardrails: stop the stage if self-referral blocks exceed 1% or credit
  cost passes 25% of referred fee revenue.
- Monitoring: dashboard of referrals, credits and blocks; an alert to the
  on-call engineer when credits per hour pass 3 times last week's average.
- Runbook: `referrals_enabled` off stops new credit at once; credit already
  paid stays.
- Clean-up and review: cards R7 and R8.

**9. Sign-off**

| Area | Person | Needed for | Status |
|---|---|---|---|
| Owner | Product lead | everything | approved 2026-03-05 |
| Finance | Finance lead | credit accounting, guardrail | approved 2026-03-05 |
| Privacy | DPO | referral tokens, device check | approved 2026-03-06 |

**11. Cards**

| Card | Repo | Content | Risk | Size | Depends on |
|---|---|---|---|---|---|
| R0 | — | Overview | — | — | — |
| R1 | API | Referral tokens, abuse checks, switch | High | M | — |
| R2 | API | Credit on first appointment, ledger entries | High | M | R1 |
| R3 | Web | Share link, credit balance | Medium | S | R1 |
| R4 | App | Same in the app; forced update | High | M | R1 |
| R5 | Admin | Referral list, blocks, manual reversal | Medium | S | R2 |
| R6 | — | Launch stages and guardrail checks | High | S | R1–R5 |
| R7 | API, Web, App | Clean-up: remove `referrals_enabled` after full rollout; due 4 weeks after stage 3 | Low | S | R6 |
| R8 | — | Post-launch review at 30 and 90 days against section 1 | — | S | R6 |

## One card in full

```markdown
**Part of the REFERRALS feature** (overview: R0). Spec sections 5.2-5.4,
decisions Q1, Q3, Q6.

**Risk: High** (money: credit is a liability on the ledger). **Size: M**.

## Scope (API)
- `ReferralCreditService::onAppointmentCompleted()` called from
  `AppointmentCompletedListener`; credits both parties once, in one
  transaction with the ledger entries.
- Refuses when referrer and friend share a payment card fingerprint or
  device id; logs `referral.blocked` with the reason.
- Nothing happens while `referrals_enabled` is off.

## Done when
- Tests: credit once per pair; no credit on a cancelled first booking; block
  on shared card or device; nothing while the switch is off; ledger balances.
- Manual: on staging, a referred test customer completes an appointment and
  both balances show 10.
- Full suite green.

## Depends on
R1.
```

## One decision record

```markdown
# 12. Referral credit is paid after the first appointment, not at booking

Date: 2026-03-02. Status: accepted. Spec: referrals, decision Q3.

## Context
Bookings can be cancelled with a full refund until 48 hours before the
appointment, so credit at booking can be gained without revenue.

## Decision
Credit both parties when the friend's first appointment is marked completed.

## Alternatives
At booking with a clawback on cancel (more code, a negative balance case);
after the refund window (an extra scheduled job, same effect).

## Consequences
Rewards arrive days later; the completion event must stay reliable; any new
way to complete an appointment must call the referral hook.
```

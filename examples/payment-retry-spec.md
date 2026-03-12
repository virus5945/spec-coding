# Feature Spec: Payment Retry for Failed Charges

**Author:** Rina Okafor
**Date:** 2026-02-18
**Status:** Approved
**Reviewers:** Tom Brennan, Aisha Patel, Carlos Ruiz

---

## Goal

Automatically retry failed credit card charges caused by transient errors (network timeouts, processor downtime, rate limits) so that customers aren't involuntarily churned by temporary payment infrastructure problems.

## Non-goals

- Retrying hard declines (insufficient funds, stolen card, expired card). Those require the customer to update their payment method.
- Dunning emails or in-app payment update prompts. That's a separate project handled by the billing comms team.
- Retrying non-card payment methods (ACH, wire, crypto). Card only for now.
- Changing the existing billing cycle or invoice generation logic.

## Background

In January, 3.2% of charge attempts failed. Of those, roughly 40% were transient -- the card was valid but the processor returned a temporary error. We currently treat all failures the same: mark the invoice as failed and send the customer to the dunning flow. This means ~1.3% of our monthly charges go to dunning unnecessarily, and about 15% of those customers churn before updating their payment method (even though their card is fine).

Stripe, Braintree, and every major subscription platform implements automatic retry for transient failures. We should too. The billing team estimated this would recover $180K/year in revenue based on current volume.

We use Stripe as our payment processor. Stripe categorizes decline codes into hard and soft failures, which gives us a clean signal for what's retryable.

## Detailed design

### Data model changes

New columns on the `charges` table:

```sql
ALTER TABLE charges ADD COLUMN retry_count INTEGER NOT NULL DEFAULT 0;
ALTER TABLE charges ADD COLUMN next_retry_at TIMESTAMPTZ;
ALTER TABLE charges ADD COLUMN idempotency_key UUID NOT NULL DEFAULT gen_random_uuid();
ALTER TABLE charges ADD COLUMN last_failure_code VARCHAR(64);
```

### API changes

No new external API endpoints. The retry logic runs as a background job.

Internal job interface:

| Job | Schedule | Description |
|---|---|---|
| `RetryFailedCharges` | Every 30 minutes | Picks up charges where `next_retry_at <= now()` and `retry_count < max_retries` |

### UI changes

**Invoice detail page:** Show retry status on failed invoices. Display "Retrying automatically -- attempt 2 of 4" instead of immediately showing the failure state.

**Admin dashboard:** Add a "Pending retry" filter to the failed charges list so support can see what's in the retry queue.

### Key implementation details

**Retry schedule:** Exponential backoff -- 1 hour, 4 hours, 24 hours, 72 hours. Four attempts total after the initial failure.

**Idempotency:** Every charge attempt uses the same `idempotency_key` for the duration of the retry window. This prevents double-charging if our job fires twice or Stripe processes slowly.

**Transient vs. hard failure detection:** We use Stripe's `decline_code` field. Retryable codes: `processing_error`, `try_again_later`, `issuer_not_available`, `reenter_transaction`. Everything else is treated as a hard decline and exits the retry queue immediately.

**Job locking:** The `RetryFailedCharges` job uses `SELECT ... FOR UPDATE SKIP LOCKED` to prevent multiple workers from retrying the same charge concurrently.

## Acceptance criteria

```gherkin
Given a charge fails with Stripe decline code "processing_error"
When the RetryFailedCharges job runs after the first backoff interval (1 hour)
Then a new charge attempt is made using the same idempotency key
  and retry_count is incremented to 1
  and next_retry_at is set to 4 hours from now

Given a charge has failed 4 retry attempts
When the RetryFailedCharges job evaluates the charge
Then no further retry is attempted
  and the invoice is moved to the standard dunning flow

Given a charge fails with Stripe decline code "card_declined"
When the system evaluates the failure
Then no retry is scheduled
  and the invoice moves directly to dunning

Given a charge is in the retry queue
When the customer manually pays the invoice through the UI
Then the charge is removed from the retry queue
  and no further retries are attempted
```

## Edge cases

| Scenario | Expected behavior |
|---|---|
| Two retry workers pick up the same charge simultaneously | `SKIP LOCKED` prevents this. If it somehow happens, the idempotency key prevents a double charge on Stripe's side |
| The idempotency key expires at Stripe (24h) before our next retry | Generate a new idempotency key for retries that happen more than 24h after the last attempt |
| A partial capture succeeded before the error | Do not retry. Treat as a completed charge for the captured amount and create a new charge for the remainder |
| Customer downgrades plan while charge is in retry queue | Retry the original amount. Proration is handled by the next billing cycle, not the retry system |
| Stripe webhook arrives marking the charge as succeeded after we've already queued a retry | The retry job checks charge status before attempting. If it's already paid, skip and clear the queue |
| Card expires between the original attempt and a retry | Stripe returns `expired_card` (hard decline). Charge exits the retry queue and enters dunning |

## Error handling

| Error condition | Response | User impact |
|---|---|---|
| Stripe API is unreachable during retry attempt | Log the error, don't increment retry count, keep the same `next_retry_at` so the job picks it up on the next cycle | None -- the customer doesn't see retry attempts |
| Stripe returns an unknown decline code | Treat as hard decline and exit retry queue. Log for manual review | Invoice goes to dunning as it would today |
| Database lock timeout when acquiring charge for retry | Job skips the charge, picks it up on the next run | None |
| Idempotency conflict (409 from Stripe) | Log a warning. Do not increment retry count. Wait for the webhook to confirm the charge status | None -- this means a previous attempt is still processing |

## Rollback plan

1. Disable the `RetryFailedCharges` job by setting the feature flag `billing.auto_retry` to false.
2. Charges currently in the retry queue will stop retrying and age out. They won't enter dunning automatically -- run the `flush_retry_queue` management command to move them to dunning immediately.
3. The schema changes (new columns) are additive and don't affect existing queries. Leave them in place.
4. No customer communication needed since retries are invisible to the customer.

## Open questions

- [x] Should we retry charges for customers who have already contacted support about the failure? **Yes -- support can manually cancel the retry from the admin dashboard if needed.**
- [ ] Do we need a per-customer opt-out for auto-retry? Product team to decide (Aisha following up).
- [ ] What's the SLA for the retry job? If the job queue backs up, how stale can `next_retry_at` get before we consider it a problem? (Tom to define alerting thresholds.)

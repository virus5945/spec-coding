# Rollout Plan: [FEATURE NAME]

**Author:** [YOUR NAME]
**Date:** [YYYY-MM-DD]
**Feature spec:** [LINK TO SPEC]
**Feature flag:** [FLAG NAME]

---

## Feature flag configuration

| Environment | Flag state | Notes |
|---|---|---|
| Development | Enabled | Always on for local dev |
| Staging | Enabled | On for all staging users |
| Production | Percentage rollout | See canary schedule below |

## Canary schedule

| Date | Target | Percentage | Duration before next step |
|---|---|---|---|
| [DAY 1] | Internal team | 0% (staff flag only) | 1 business day |
| [DAY 2] | Canary | 5% | 2 business days |
| [DAY 4] | Expanded canary | 25% | 2 business days |
| [DAY 6] | Broad rollout | 50% | 1 business day |
| [DAY 7] | General availability | 100% | -- |

Pause the rollout and investigate if any monitoring threshold is breached at any stage.

## Monitoring

### Metrics to watch

| Metric | Baseline | Alert threshold | Dashboard link |
|---|---|---|---|
| [ERROR RATE] | [CURRENT VALUE] | [> X% increase] | [LINK] |
| [LATENCY P99] | [CURRENT VALUE] | [> X ms] | [LINK] |
| [THROUGHPUT] | [CURRENT VALUE] | [< X% of normal] | [LINK] |
| [BUSINESS METRIC] | [CURRENT VALUE] | [DESCRIBE THRESHOLD] | [LINK] |

### Alerts

- [ALERT NAME]: fires when [CONDITION]. Routed to [TEAM/CHANNEL].
- [ALERT NAME]: fires when [CONDITION]. Routed to [TEAM/CHANNEL].

## Rollback trigger criteria

Roll back immediately if any of the following occur:

- Error rate for the affected endpoint exceeds [X]% for more than [N] minutes
- P99 latency exceeds [X] ms sustained over [N] minutes
- Any data corruption or data loss is detected
- [BUSINESS-SPECIFIC TRIGGER, e.g., payment failure rate increases]

## Rollback procedure

1. Set the feature flag to 0% in [FLAG MANAGEMENT TOOL].
2. Verify the flag change propagated (check [WHERE TO CHECK]).
3. Monitor error rates for [N] minutes to confirm they return to baseline.
4. If data changes need reversal, run [SCRIPT OR PROCEDURE] (see rollback section in the feature spec).
5. Post a message in [CHANNEL] confirming the rollback and stating next steps.

## Communication plan

| Audience | When | Channel | Who sends it |
|---|---|---|---|
| Engineering team | Each rollout stage | [SLACK CHANNEL] | [NAME] |
| Support team | Before 25% rollout | [EMAIL / SLACK] | [NAME] |
| Affected customers | Before 100% rollout (if applicable) | [EMAIL / IN-APP] | [NAME] |
| Stakeholders | After 100% rollout | [EMAIL / SLACK] | [NAME] |
| All of the above | On rollback | [SLACK CHANNEL] | [NAME] |

## Open questions

- [ ] [QUESTION 1]
- [ ] [QUESTION 2]

# Incident Postmortem: [INCIDENT TITLE]

**Date of incident:** [YYYY-MM-DD]
**Author:** [YOUR NAME]
**Status:** Draft | In Review | Final
**Postmortem date:** [YYYY-MM-DD]

---

## Incident summary

| Field | Details |
|---|---|
| Severity | [SEV1 / SEV2 / SEV3 / SEV4] |
| Duration | [START TIME] to [END TIME] ([TOTAL DURATION]) |
| Services affected | [LIST AFFECTED SERVICES] |
| Users affected | [NUMBER OR PERCENTAGE OF USERS] |
| Revenue impact | [ESTIMATED LOSS OR "NONE"] |
| Detection method | [MONITORING ALERT / CUSTOMER REPORT / MANUAL DISCOVERY] |
| Time to detect | [MINUTES FROM START TO FIRST ALERT] |
| Time to resolve | [MINUTES FROM FIRST ALERT TO FULL RESOLUTION] |

## What happened

[2-3 paragraphs describing the incident in plain language. What broke, what users experienced, and how it was fixed. Write this so someone outside the team can understand it.]

## Timeline

All times in [TIMEZONE].

| Time | Event |
|---|---|
| [HH:MM] | [FIRST SIGN OF TROUBLE -- what triggered or started the incident] |
| [HH:MM] | [ALERT FIRES OR SOMEONE NOTICES] |
| [HH:MM] | [FIRST RESPONSE ACTION] |
| [HH:MM] | [INVESTIGATION STEP OR ESCALATION] |
| [HH:MM] | [ROOT CAUSE IDENTIFIED] |
| [HH:MM] | [FIX DEPLOYED OR MITIGATION APPLIED] |
| [HH:MM] | [SERVICE FULLY RESTORED] |
| [HH:MM] | [MONITORING CONFIRMS RECOVERY] |

## Root cause analysis

[Describe the technical root cause. Be specific. "The database was slow" is not a root cause. "A missing index on the `orders.user_id` column caused a full table scan when the batch job ran at 02:00 UTC" is.]

## Contributing factors

- [FACTOR 1 -- e.g., monitoring gap, missing test, config drift]
- [FACTOR 2 -- e.g., incomplete runbook, unclear ownership]
- [FACTOR 3 -- e.g., recent change that introduced the bug]

## What went well

- [SOMETHING THAT WORKED -- fast detection, good communication, effective runbook, etc.]
- [SOMETHING ELSE THAT WORKED]

## What went poorly

- [SOMETHING THAT DIDN'T WORK -- slow escalation, missing monitoring, confusing logs, etc.]
- [SOMETHING ELSE THAT DIDN'T WORK]

## Action items

| Action | Owner | Due date | Status |
|---|---|---|---|
| [FIX THE ROOT CAUSE] | [NAME] | [YYYY-MM-DD] | Not started |
| [ADD MONITORING / ALERTING] | [NAME] | [YYYY-MM-DD] | Not started |
| [UPDATE RUNBOOK OR DOCUMENTATION] | [NAME] | [YYYY-MM-DD] | Not started |
| [IMPROVE TESTING] | [NAME] | [YYYY-MM-DD] | Not started |
| [ADDRESS CONTRIBUTING FACTOR] | [NAME] | [YYYY-MM-DD] | Not started |

## Lessons learned

[2-3 honest takeaways. What did this incident teach you about your systems, processes, or assumptions? Avoid generic statements like "we need better monitoring." Be specific about what kind of monitoring and why it would have helped.]

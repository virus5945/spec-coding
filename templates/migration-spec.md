# Migration Spec: [MIGRATION NAME]

**Author:** [YOUR NAME]
**Date:** [YYYY-MM-DD]
**Status:** Draft | In Review | Approved | Executed
**Reviewers:** [LIST REVIEWERS]

---

## Migration goal

[One or two sentences. What data or schema is changing and why? Link to the feature spec or ticket that requires this migration.]

## Schema changes

### Before

```sql
[EXISTING TABLE / SCHEMA DEFINITION]
```

### After

```sql
[NEW TABLE / SCHEMA DEFINITION -- highlight what changed]
```

### Summary of changes

| Change | Table | Column / Index | Details |
|---|---|---|---|
| [ADD / DROP / ALTER / RENAME] | [TABLE_NAME] | [COLUMN_NAME] | [BRIEF DESCRIPTION] |
| [ADD / DROP / ALTER / RENAME] | [TABLE_NAME] | [COLUMN_NAME] | [BRIEF DESCRIPTION] |

## Data transformation rules

[Describe how existing data maps to the new schema. If this is a schema-only change with no data backfill, say "No data transformation needed."]

| Source | Destination | Transformation |
|---|---|---|
| [OLD_TABLE.OLD_COLUMN] | [NEW_TABLE.NEW_COLUMN] | [COPY AS-IS / CONVERT / SPLIT / DEFAULT VALUE / etc.] |
| [OLD_TABLE.OLD_COLUMN] | [NEW_TABLE.NEW_COLUMN] | [COPY AS-IS / CONVERT / SPLIT / DEFAULT VALUE / etc.] |

### Null handling

[What happens to rows where the source value is null? Describe defaults or skip behavior.]

### Data volume

- Estimated rows affected: [NUMBER]
- Estimated migration duration: [MINUTES / HOURS]
- Will the migration lock tables? [YES / NO -- if yes, for how long?]

## Rollback procedure

### Schema rollback

```sql
[SQL STATEMENTS TO REVERSE THE SCHEMA CHANGES]
```

### Data rollback

[How to restore the original data. Options: restore from backup, reverse transformation script, or "not reversible -- explain why."]

### Rollback decision criteria

[Under what conditions do you roll back? Failed validation checks, error rate threshold, etc.]

## Performance impact

| Concern | Assessment |
|---|---|
| Table locks | [DURATION AND AFFECTED TABLES] |
| Disk usage increase | [ESTIMATED SIZE CHANGE] |
| Query performance | [ANY QUERIES THAT WILL SLOW DOWN OR SPEED UP] |
| Connection pool pressure | [WILL THE MIGRATION SATURATE CONNECTIONS?] |
| Replication lag | [EXPECTED LAG ON READ REPLICAS] |

## Testing plan

### Local validation

- [ ] Migration runs without errors on a local copy of the database
- [ ] Rollback script runs without errors
- [ ] Application code works with both old and new schema (if deploying schema first)

### Staging validation

| Step | How to verify | Expected result |
|---|---|---|
| Run migration | [COMMAND OR PROCESS] | [WHAT SUCCESS LOOKS LIKE] |
| Verify row counts | [QUERY TO RUN] | [EXPECTED COUNT] |
| Verify data integrity | [QUERY OR SPOT CHECK] | [EXPECTED VALUES] |
| Run application smoke tests | [WHICH TESTS] | [ALL PASSING] |
| Verify rollback | [COMMAND OR PROCESS] | [CLEAN REVERSAL] |

### Production execution

- Maintenance window required: [YES / NO]
- Estimated downtime: [NONE / DURATION]
- Who runs the migration: [NAME]
- Who monitors during execution: [NAME]
- Communication plan: [HOW YOU NOTIFY USERS IF THERE IS DOWNTIME]

## Open questions

- [ ] [QUESTION 1]
- [ ] [QUESTION 2]

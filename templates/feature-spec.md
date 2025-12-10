# Feature Spec: [FEATURE NAME]

**Author:** [YOUR NAME]
**Date:** [YYYY-MM-DD]
**Status:** Draft | In Review | Approved | Implemented
**Reviewers:** [LIST REVIEWERS]

---

## Goal

[One or two sentences. What are you building and why? Focus on the user problem, not the implementation.]

## Non-goals

- [Something people might assume is in scope but isn't]
- [Another thing you're explicitly not doing]
- [Be specific -- vague non-goals don't help]

## Background

[2-3 paragraphs of context. Why now? What has changed? What did you try before? Link to any prior discussions, metrics, or support tickets that motivated this work. Write this for someone joining the project next month.]

## Detailed design

### Data model changes

[Describe new tables, columns, or document schema changes. If nothing changes, say "No data model changes."]

```
[TABLE OR SCHEMA DEFINITION]
```

### API changes

[New or modified endpoints. Include method, path, and a brief description.]

| Method | Path | Description |
|---|---|---|
| [POST] | [/api/resource] | [What it does] |

### UI changes

[Describe what the user sees. Reference wireframes or mockups if you have them. If this is a backend-only change, say "No UI changes."]

### Key implementation details

[Anything non-obvious about how this works. Algorithms, third-party dependencies, performance considerations. Skip this section if the implementation is straightforward.]

## Acceptance criteria

```gherkin
Given [PRECONDITION]
When [ACTION]
Then [EXPECTED RESULT]

Given [PRECONDITION]
When [ACTION]
Then [EXPECTED RESULT]

Given [PRECONDITION]
When [ACTION]
Then [EXPECTED RESULT]

Given [PRECONDITION]
When [ACTION]
Then [EXPECTED RESULT]
```

## Edge cases

| Scenario | Expected behavior |
|---|---|
| [EDGE CASE 1] | [WHAT SHOULD HAPPEN] |
| [EDGE CASE 2] | [WHAT SHOULD HAPPEN] |
| [EDGE CASE 3] | [WHAT SHOULD HAPPEN] |
| [EDGE CASE 4] | [WHAT SHOULD HAPPEN] |

## Error handling

| Error condition | Response | User impact |
|---|---|---|
| [CONDITION 1] | [SYSTEM RESPONSE] | [WHAT THE USER SEES OR EXPERIENCES] |
| [CONDITION 2] | [SYSTEM RESPONSE] | [WHAT THE USER SEES OR EXPERIENCES] |
| [CONDITION 3] | [SYSTEM RESPONSE] | [WHAT THE USER SEES OR EXPERIENCES] |

## Rollback plan

[How do you undo this if something goes wrong after release? Be specific. "Revert the PR" is fine if that's actually sufficient. If there are data migrations involved, describe how to reverse them.]

## Open questions

- [ ] [QUESTION 1 -- who needs to answer it?]
- [ ] [QUESTION 2 -- who needs to answer it?]
- [ ] [QUESTION 3 -- who needs to answer it?]

# Spec Review Checklist

Use this checklist when reviewing a spec before approving it. Not every item applies to every spec -- skip what's irrelevant, but think twice before skipping.

---

## Scope clarity

- [ ] The goal is specific enough that you could write a test for it
- [ ] Non-goals are listed and actually rule things out (not just restating the goal in negative form)
- [ ] There's no hidden scope buried in "Key implementation details" that should be its own spec
- [ ] The background section gives enough context for someone joining the team next month

## Edge cases

- [ ] Null, empty, and zero-value inputs are addressed
- [ ] Concurrent access or duplicate requests are handled
- [ ] Permission and authorization boundaries are defined
- [ ] Error paths have expected behavior, not just "return 500"
- [ ] Behavior at system limits is specified (max file size, max records, timeout)

## Rollback

- [ ] A rollback plan exists and it's more specific than "revert the PR"
- [ ] If there's a data migration, it's reversible or the spec explains why not
- [ ] Feature flag or kill switch is documented if the feature can be turned off

## Testability

- [ ] Acceptance criteria are in Given/When/Then format (or equivalently precise)
- [ ] A QA engineer could run through the spec without asking the author questions
- [ ] Happy path and at least 3 failure paths are covered
- [ ] Performance expectations are stated if load is a concern

## Dependencies

- [ ] External systems and services are listed
- [ ] Failure modes for each dependency are documented (what if it's down?)
- [ ] API contracts or data formats for cross-team interfaces are specified
- [ ] Timeline dependencies or ordering constraints are called out

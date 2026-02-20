# When to write a spec (and when not to)

## Write a spec when the work:

- **Crosses team boundaries.** If another team needs to build something, change something, or review something for your feature to ship, write it down. Verbal agreements evaporate.
- **Has failure modes.** If a user can lose data, get charged twice, or see stale state, you need to think through the edge cases before you code them.
- **Touches stored data.** Schema changes, migrations, and data backfills deserve a written plan because the rollback cost is high.
- **Takes more than two days.** If you'll forget the details by Wednesday, your reviewer will never know them. Write them down.

## Skip the spec when the work is:

- **A CSS or copy change.** Ship it. If the button is 2px off, you don't need a design doc.
- **A dependency update.** Bump the version, run the tests, open the PR.
- **A one-line config fix.** The PR description is the spec.
- **A direct bug fix with an obvious cause.** If the stack trace tells you everything, just fix it and write a good commit message.

## When in doubt

Ask yourself: "If this goes wrong, how bad is it?" If the answer involves customer data, money, or a 3am page, write the spec. If the worst case is a quick revert, skip it.

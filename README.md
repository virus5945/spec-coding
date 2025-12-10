# spec-kit

Markdown templates for spec-first software development.

## What's inside

| Template | Purpose |
|---|---|
| [Feature Spec](templates/feature-spec.md) | Define a feature before building it |
| [API Contract](templates/api-contract.md) | Lock down an API interface between teams |
| [Migration Spec](templates/migration-spec.md) | Plan a database or data migration |
| [Incident Postmortem](templates/incident-postmortem.md) | Document what went wrong and what to fix |

See [examples/](examples/) for a filled-in feature spec.

## Usage

1. Copy the template you need into your project or wiki.
2. Fill in the `[BRACKETED]` placeholders with your specifics.
3. Review the draft with your team before writing code.
4. Update the spec as decisions change during implementation.

That's it. No tooling required.

## Philosophy

Most rework happens because the problem wasn't well-defined before someone started coding. A short spec forces you to think through edge cases, failure modes, and scope boundaries while changes are still cheap -- before anything is built. The spec doesn't need to be perfect. It needs to be written down so your team can poke holes in it before those holes show up in production.

## Contributing

Contributions are welcome. If you have a template that your team actually uses and finds useful, open a pull request. Keep templates practical and concise -- nobody reads a 20-page spec.

## License

[MIT](LICENSE)

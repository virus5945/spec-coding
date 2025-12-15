# Contributing to spec-kit

## Submitting a template

Open a pull request with your template in the `templates/` directory. Good templates come from real usage -- if your team has been using a spec format that works, share it.

### What we're looking for

- **Practical.** Every section should exist because someone will actually fill it in. Remove sections that people consistently skip.
- **Concise.** If a template is longer than 2 printed pages, it's too long. People won't fill out a 20-section document for a feature that takes a week to build.
- **Bracketed placeholders.** Use `[PLACEHOLDER TEXT]` for values the user fills in. Add a brief hint inside the brackets so people know what goes there.

### Formatting conventions

- Use ATX-style headings (`# H1`, `## H2`, etc.)
- Use pipe tables for structured data
- Put code examples in fenced code blocks with a language hint
- Keep lines under 120 characters where possible
- End files with a single newline

### Review process

1. Open a PR with a clear description of what the template covers and why it's useful.
2. A maintainer will review within a few days. Expect feedback on length and clarity.
3. If the template includes a filled-in example, add it to `examples/`. This is optional but helpful.

### Filing issues

Found a gap in an existing template? Open an issue describing what's missing and why it matters. Include a real scenario if you can -- "I was speccing X and couldn't capture Y" is more useful than "add a section for Y."

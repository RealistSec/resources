# Developer Guide – GraphQL Schema Design Documenter

## Architecture
- Static HTML. Regex counts types, enums, inputs, and extracts Query/Mutation fields to generate notes.
- Guidance strings are simple heuristics.

## Extending
- Add subscriptions and directives counts.
- Render markdown export of the summary.

## Testing
- Paste schemas with and without mutations to ensure the note toggles appropriately.
- Playwright capture after installing playwright:
  - `npx playwright install chromium`
  - `node ../scripts/snap.js graphql-schema-design-documenter/index.html`

## Verification artifacts
- Screenshot: `docs/screenshot.png`.
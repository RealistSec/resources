# Developer Guide – GraphQL Query Complexity Analyzer

## Architecture
- Static HTML with inline CSS/JS; no network calls.
- Complexity is estimated by counting fields, maximum brace depth, and presence of pagination arguments.
- Risk level is derived from the score thresholds (Low/Medium/High).

## Extending
- Adjust weighting by tweaking the coefficients in `analyze()`.
- Add schema awareness by parsing type definitions and penalizing nested lists.
- Wire to a server-side endpoint if real validation is needed.

## Testing
- Manual: paste sample queries (simple, deep, many fields) and observe score changes.
- Playwright capture after installing playwright:
  - `npx playwright install chromium`
  - `node ../scripts/snap.js graphql-query-complexity-analyzer/index.html`

## Verification artifacts
- Screenshot: `docs/screenshot.png`.
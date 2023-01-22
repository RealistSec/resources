# Developer Guide – Raw HTTP Security Header Analyzer

## Architecture
- Single-page HTML with inline CSS/JS.
- Expected headers are stored in the `expectations` map.
- The analyzer parses raw header lines and reports missing keys.

## Extending
- Add more headers or custom recommendations by expanding `expectations`.
- Add severity scoring (info/warn/error) if needed.

## Testing
- Manual: remove a header to confirm it is flagged; add all to see only green checks.
- Playwright capture after installing playwright:
  - `npx playwright install chromium`
  - `node ../scripts/snap.js raw-http-security-header-analyzer/index.html`

## Verification artifacts
- Screenshot: `docs/screenshot.png`.
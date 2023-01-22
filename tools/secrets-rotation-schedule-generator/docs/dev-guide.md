# Developer Guide – Secrets Rotation Schedule Generator

## Architecture
- Static HTML. Cadence defaults per secret type stored in `cadences` map.
- Generates bullet list describing rotation and overlap guidance.

## Extending
- Add calendar export (ICS) or reminders via downloadable JSON.
- Allow custom cadences per environment.

## Testing
- Switch secret types to confirm cadence changes (30/60/90 days).
- Playwright capture after installing playwright:
  - `npx playwright install chromium`
  - `node ../scripts/snap.js secrets-rotation-schedule-generator/index.html`

## Verification artifacts
- Screenshot: `docs/screenshot.png`.
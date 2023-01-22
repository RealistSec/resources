# Developer Guide – Rate Limiting Visualizer

## Architecture
- Static HTML; simulation runs in `simulate()` using a simple token bucket model.
- Tokens refill by `rate` each tick, capped by `burst`.

## Extending
- Visualize as a chart using `<canvas>`.
- Add leaky bucket or sliding window models for comparison.

## Testing
- Set burst=0 to ensure output still renders gracefully.
- Playwright capture after installing playwright:
  - `npx playwright install chromium`
  - `node ../scripts/snap.js rate-limiting-visualizer/index.html`

## Verification artifacts
- Screenshot: `docs/screenshot.png`.
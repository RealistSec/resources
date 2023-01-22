# Developer Guide – Data Transfer Time Estimator

## Architecture
- Static HTML; calculation lives in `estimate()`.
- Converts Mbps to Mb/s and computes `seconds = size(MB)*8 / effectiveMbps`.
- Formats human-readable durations in `humanTime()`.

## Extending
- Add presets for satellite/LoRa or custom RTT to estimate TCP slow-start.
- Add graph showing time vs. bandwidth by plotting multiple points.

## Testing
- Manual sanity: 8 Mb over 8 Mbps ≈ 1 second.
- Playwright capture after installing playwright:
  - `npx playwright install chromium`
  - `node ../scripts/snap.js data-transfer-time-estimator/index.html`

## Verification artifacts
- Screenshot: `docs/screenshot.png`.
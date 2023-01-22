# User Guide – Rate Limiting Visualizer

## Goal
Model a token bucket limiter with burst capacity.

## Steps
1. Open `index.html`.
2. Set **Rate** (requests/sec), **Burst size**, and **Duration**.
3. Click **Simulate**.
4. Read the timeline showing allowed requests and remaining tokens per second.

## Tips
- Burst should be >= rate to allow brief spikes.
- Lower rate and burst for stricter APIs.
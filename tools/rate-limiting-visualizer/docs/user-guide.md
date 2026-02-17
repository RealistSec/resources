# User Guide — Rate Limiting Visualizer

> Learn how to use the Rate Limiting Visualizer to understand and compare rate limiting algorithms.

---

## Getting Started

1. **Open** `index.html` in any modern web browser (Chrome, Firefox, Safari, Edge)
2. The tool loads instantly — no installation, no server, no dependencies required

---

## Interface Overview

### Algorithm Tabs
Select from four rate limiting algorithms at the top of the page:
- **🪣 Token Bucket** — Tokens refill at a steady rate; each request consumes one
- **🔄 Sliding Window** — Counts requests in a continuously sliding time window
- **📊 Fixed Window** — Counts requests in fixed time intervals that reset at boundaries
- **💧 Leaky Bucket** — Queues requests and processes them at a constant leak rate

### Configuration Panel (Left Side)
- **Algorithm-specific inputs** change dynamically when you switch tabs
- **Traffic Pattern** — Choose how incoming requests behave:
  - *Steady* — constant rate throughout
  - *Burst* — periodic spikes every 4 seconds
  - *Random* — Poisson-distributed randomness
  - *Ramp-up* — gradually increasing traffic
  - *Single Spike* — one big spike mid-simulation
- **Duration** — How long the simulation runs (3–60 seconds)
- **Incoming Request Rate** — Baseline requests per second

### SVG Visualizer (Main Area)
- Shows a real-time animated diagram of the selected algorithm
- Token Bucket displays tokens as circles inside a bucket with water-level fill
- Leaky Bucket shows drip animations from the bottom
- Sliding/Fixed windows show highlighted segments on a timeline

### Stats Dashboard
Four cards tracking live statistics:
- **Total Requests** — all incoming requests
- **Allowed** — requests that passed the rate limit (green)
- **Rejected** — requests that were blocked (red)
- **Accept Rate** — percentage allowed

### Timeline Chart
A bar chart showing allowed (green) vs rejected (red) counts per second over the full simulation duration.

### Code Snippets
Ready-to-copy rate limiting configurations for:
- **Nginx** — `limit_req_zone` configuration
- **Express.js** — `express-rate-limit` middleware
- **Cloudflare Workers** — rate limiter binding

---

## Tips

- **Compare algorithms** by running each with the same settings and traffic pattern
- **Use Burst or Spike** traffic to see how each algorithm handles surges
- **Increase request rate** beyond capacity to observe rejection behaviour
- **Toggle dark mode** via the switch in the top-right corner
- **Works offline** — save to USB and open on any computer, no internet required

---

## FAQ

Expand the FAQ section at the bottom of the page for answers to common questions about rate limiting concepts and this tool.
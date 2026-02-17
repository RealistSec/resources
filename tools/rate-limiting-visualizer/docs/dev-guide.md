# Developer Guide — Rate Limiting Visualizer

> Technical overview for contributors and developers looking to extend or fork this tool.

---

## Architecture

The tool is a **single standalone HTML file** (`index.html`) containing:

| Section | Purpose |
|---------|---------|
| `<head>` | SEO meta tags, JSON-LD schemas, Google Fonts link, `<style>` block |
| `<body>` HTML | Semantic layout: header, tabs, control panel, SVG containers, FAQ, footer |
| `<script>` | Complete simulation engine, SVG rendering, event handlers |

**No build step. No bundler. No framework.**

---

## Code Structure (within the `<script>` block)

```
State (S object)          — Global simulation state
Theme Toggle              — localStorage-persisted light/dark switch
Algorithm Definitions     — ALGOS config object (fields, descriptions)
Tab Switching             — Event listeners for algorithm and snippet tabs
Traffic Generators        — genTraffic() with 5 patterns
Simulation Engines        — simStep_tokenBucket(), _slidingWindow(), etc.
SVG Helpers               — svgEl(), clearSvg(), cssVar()
Idle Visualizations       — drawIdle*() functions for each algorithm
Live Visualizations       — drawLive*() functions during simulation
Timeline Chart            — drawTimeline() bar chart
Stats Update              — updateStats()
Simulation Runner         — runSim(), stopSim(), resetSim()
Code Snippets             — SNIPPETS object + showSnippet()
FAQ Builder               — FAQ_DATA array + buildFAQ()
Init                      — IIFE that sets up everything on page load
```

---

## Adding a New Algorithm

1. Add a new entry to the `ALGOS` object with `label`, `desc`, and `fields`
2. Add a new `<button>` with `data-algo="your-algo"` in the tabs `<nav>`
3. Create `simStep_yourAlgo(nReqs, t)` returning `{allowed, rejected, ...}`
4. Create `drawIdleYourAlgo()` and `drawLiveYourAlgo()` SVG renderers
5. Add cases to the `switch` statements in `drawIdleViz()`, `drawLiveViz()`, and `runSim()`

---

## SEO Notes

- **Canonical URL**: `https://realistsec.com/resources/rate-limiting-visualizer`
- **JSON-LD schemas**: `WebApplication` + `FAQPage` — keep the FAQ data in the `<head>` JSON-LD in sync with the `FAQ_DATA` array in the script
- **OG/Twitter tags**: Update `og:image` when a social preview image is available
- **Heading hierarchy**: Single `<h1>` in the header, `<h2>` for FAQ section

---

## Offline Considerations

- **Google Fonts** loaded via `<link>` with `font-display: swap` — falls back to `system-ui, -apple-system, sans-serif` when offline
- **Zero external JS** — all logic is inline vanilla JavaScript
- **Clipboard API** has a fallback using `document.execCommand('copy')` for older browsers
- **localStorage** used only for theme preference — non-critical, no errors if blocked

---

## Performance

- SVG elements are created/destroyed each simulation tick (clearSvg + rebuild) — simple and avoids DOM leak
- `setInterval` at 700ms per tick keeps CPU usage minimal
- CSS transitions handle theme switching animations
- No canvas, no WebGL, no heavy rendering libraries

---

## Testing

1. Open `index.html` in a browser
2. Run each algorithm with each traffic pattern
3. Verify stats add up: `total = allowed + rejected`
4. Toggle theme and verify colours update
5. Test code copy button
6. Test FAQ accordion open/close
7. Disconnect network and verify the tool still functions
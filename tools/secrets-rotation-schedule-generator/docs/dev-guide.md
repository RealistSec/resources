# Developer Guide – Secrets Rotation Schedule Generator

## Architecture
The tool is built as a **Single Page Application (SPA)** contained entirely within one HTML file.
- **No Build Step**: Works directly in the browser.
- **No Dependencies**: Pure Vanilla JS, CSS, and SVG. No external frameworks (React, Vue, etc.) or CDNs required for core functionality.
- **State Management**: Uses a simple reactive state object `state = { ... }` that triggers UI updates via `generateSchedule()`.

## Customization

### Adding a New Secret Type
To add a new secret type, modify the `configMap` object in the `<script>` section of `index.html`:

```javascript
const configMap = {
    // ... existing types
    my_new_type: { 
        name: "My Custom Secret", 
        defaultCadence: 45, 
        riskFactor: 1.1 
    }
};
```

Then add a corresponding `<option>` to the `#secretType` select element in the HTML.

### Tuning Risk Scoring
The risk algorithm is located in the `analyzeRisk()` function. It calculates a score based on:
- **Cadence length** (longer = higher risk)
- **Environment multiplier** (Production = 1.5x)
- **Type risk factor** (defined in `configMap`)

### Styling
All styles are defined in the `<style>` block.
- **CSS Variables**: Check `:root` for color palette and font settings.
- **Dark Mode**: Controlled via `[data-theme="dark"]` selector.

## Local Development
1. Open `index.html` in Chrome/Firefox/Edge.
2. Make changes to the code.
3. Refresh the browser to see updates.

## SEO & Accessibility
- **JSON-LD**: Structured data is injected at the bottom of the `<body>`.
- **Semantic HTML**: Uses `<main>`, `<section>`, `<header>`, and `<footer>` landmarks.
- **Contrast**: Colors are tested against WCAG AA standards.
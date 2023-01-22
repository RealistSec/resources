# User Guide – GraphQL Query Complexity Analyzer

## Goal
Estimate the relative cost of a GraphQL query by looking at depth, field counts, and list arguments.

## Steps
1. Open `index.html`.
2. Paste your GraphQL operation into the textarea.
3. Click **Analyze**.
4. Review the **Estimated complexity**, depth, list usage, and notes.
5. Trim selections or add pagination where suggested.

## Notes
- This is a client-side heuristic, not an execution-time profiler.
- Always enforce limits server-side (depth, cost, timeout).
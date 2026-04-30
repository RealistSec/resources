# Tools Suite Agent Notes

Scope: `/home/nick/Desktop/resources/tools`

This folder is a portfolio-style suite of **seven standalone static mini apps**:

- `ai-data-readiness`
- `data-transfer-time-estimator`
- `graphql-query-complexity-analyzer`
- `graphql-schema-design-documenter`
- `rate-limiting-visualizer`
- `raw-http-security-header-analyzer`
- `secrets-rotation-schedule-generator`

## Source of truth

- Product requirements: `docs/PRD.yaml`
- Plan-specific standards: `docs/plan/20260430-tools-suite-rebuild/implementation-standards.md`
- Portfolio surface to keep in sync: `index.html`

## Non-negotiable conventions

1. **Each tool stays standalone and statically hostable.** No server, auth flow, or mandatory build pipeline.
2. **Real client-side functionality only.** No placeholder outputs, fake scores, or dead controls.
3. **Each rebuilt tool must add at least two meaningful new features** beyond its current baseline.
4. **Shared suite UX is required.** Keep layout, spacing, typography, accessibility patterns, and interaction states consistent across all seven tools.
5. **Offline/local-first by default where applicable.** Prefer in-browser parsing, calculation, transformation, and export.
6. **Portfolio integration is part of done.** When a tool changes meaningfully, update `tools/index.html` card copy and screenshot reference if needed.

## Required deliverables per tool

- `index.html` — working app
- `README.md`
- `docs/user-guide.md`
- `docs/dev-guide.md`
- `docs/screenshot.png`

## Quality bar

- Responsive from mobile through desktop
- Keyboard-usable primary flows
- Sufficient color contrast and visible focus states
- Clear empty, error, and success states
- Prefer local sample/demo input over remote dependencies

If an implementation choice conflicts with these notes, follow `docs/PRD.yaml` and the plan standards doc before inventing a one-off pattern.
# Architecture Screenshots

These are unmodified screenshots captured from the user's live Opal session. They show the app configuration and generated UI, but are not proof that the report claims are accurate or that the full workflow and Critic completed successfully.

1. `01-live-canvas-dashboard-config.png` — IntelEdge draft canvas and dashboard output prompt. The dashboard references Evidence Critic and Research Brief.
2. `02-research-brief-config.png` — User Input prompt requesting a company or technology and optional market/competitors.
3. `03-google-sheets-node-config.png` — Google Sheets output-node configuration and its Evidence Critic reference; no Sheet row is shown.
4. `04-generated-card-unverified.png` — Cloudflare intelligence card preview. It contains highly specific claims that have not been verified; treat this as UI-rendering evidence only.

The Console was inspected during debugging. Its visible captures showed Agent Session/task-planning details but did not show exact returned source URLs or a completed Critic verdict. Raw Console screenshots were not copied here because they contain internal session/cache identifiers.

Still needed for a complete submission:

- Native Opal workflow export saved as `workflow.json`.
- Console evidence showing returned source URLs/excerpts and a completed Evidence Critic review.
- Published input form, if distinct from the captured editor prompt.
- Actual Google Sheet with the reviewed test row.
- A verified run with unsupported claims rejected or marked unknown.

Do not present the generated Cloudflare report as verified research until its claims and citations have been checked.

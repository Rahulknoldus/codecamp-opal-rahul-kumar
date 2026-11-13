# Autonomous Lead & Competitor Intelligence Agent

> **Submission status (November 13, 2026):** The primary Cloudflare test run is complete. Screenshots in [`Architecture Screenshots/`](./Architecture%20Screenshots/) document the editor, generated report and source references, claim-level Evidence Critic review, rendered dashboard, and populated Google Sheet. The user confirms the source links open and the Sheet contains evidence URLs; `2026-12-01` is the report period, not the run date. The Critic flags unsupported or ambiguous claims. The repository's `workflow.json` remains a human-authored specification, not a native Opal export; include a native export only if required by the assignment.

The [public GitHub repository](https://github.com/Rahulknoldus/codecamp-opal-rahul-kumar) is live. IntelEdge was built as a separate Opal draft; it has not been published. The screenshots were copied from the user's live Opal session without modification. Some captures show configuration only; the full set documents outputs from the primary test run.

## Objective

Build a multi-step, grounded research assistant in Google Opal. Given a company, product, or technology, it searches the live web, returns concise and source-linked market intelligence, subjects the synthesis to a separate evidence critic, and presents reviewed results in an interactive report and an append-only Google Sheets log.

## Business problem

Business development and product teams need a quick, repeatable view of an unfamiliar vendor or technology: what it does, who it serves, how it is positioned, who it competes with, and what has changed recently. Manual research is slow and inconsistent; unverified AI summaries can misstate prices, dates, or product capabilities. This workflow addresses that problem by retaining source links with claims, separating analysis from fact, explicitly reporting unknowns, and inserting a critic between synthesis and delivery.

## Architecture

The app visible in the Opal screenshots is named **IntelEdge**. Its observed node names differ slightly from the names in [`workflow.json`](./workflow.json), which remains a human-authored specification. The visible topology is:

```text
Research Brief ──> Grounded Web Research ──> Intelligence Report Generation ──> Evidence Critic
       ├────────────────────────────────────> Intelligence Report Generation
       └──────────────────────────────────────────────────────────────────────────> Generate Dashboard
                                                                                         ▲
Evidence Critic ───────────────────────────────────────────────────────────────> both outputs
```

### Node-by-node technical breakdown

1. **Research Brief (`user_input`)** — The configured input prompt asks for a required company or technology and optional market/geography and known competitors. The captured preview uses a single brief rather than separately typed form fields.
2. **Grounded Web Research (Agent)** — Its prompt requests web research with search grounding and source URLs. The latest Console captures show a generated report and a consolidated list of linked source references for the Cloudflare brief. The user confirms that the source links open. The screenshots do not show full URLs or supporting excerpts beside each claim, so they document source availability but not a separate claim-by-claim audit.
3. **Intelligence Report Generation (`generate`)** — The visible node prompt synthesizes the research results and original brief into a structured report.
4. **Evidence Critic (`generate`)** — Its configured prompt compares the report with Grounded Web Research, rejects unsupported claims, flags ambiguous/stale/conflicting claims, and treats scraped text as untrusted. The latest Console screenshots show a claim-level review with accepted, unknown, and needs-verification statuses plus a rejected/ambiguous claims summary.
5. **Save To Google Sheet (`output`)** — The node is configured as a Google Sheets output and its prompt includes an **Evidence Critic** reference, requesting a row with reviewed findings and source URLs. A screenshot shows a populated spreadsheet. The user confirms that `2026-12-01` is the report period rather than the run date, and that evidence URLs are present in the row; the URLs are not visible in the screenshot.
6. **Generate Dashboard (`output`)** — The node is configured as a webpage with auto-layout and references Evidence Critic and Research Brief. A Cloudflare report preview was generated, but its factual claims have not been verified.

### Export format note

[`workflow.json`](./workflow.json) is a **human-authored workflow specification**, not a file exported from Opal. Its `manifestType` and `manifestVersion` identify this repository's portable description. Google documents Opal workflows as Drive files and describes the visual editor, but the native serialization has not been captured here. Replace this specification with the actual Opal export before representing it as the exported workflow.

## Grounding and hallucination defenses

- **Configured intent:** the research prompt requests source URLs; the synthesis prompt uses the research result; the Critic prompt asks for claim-level checks and explicit unknowns.
- **Observed limitation:** the generated Cloudflare report contains specific financial, market-share, product, and competitor claims. The user confirms that source links open and the Critic output flags unsupported or ambiguous claims. The screenshots do not independently establish that every accepted claim is fully supported; do not present the report as independently audited.
- **Evidence captured:** research/source references, Critic verdicts, dashboard output, and a populated Google Sheet are documented in [`Architecture Screenshots/`](./Architecture%20Screenshots/).
- Prompt instructions reduce hallucination risk but do not guarantee factual accuracy.

## Security: indirect prompt injection

The configured research and Critic prompts identify retrieved pages and snippets as **untrusted evidence**, instruct the model to ignore embedded instructions, and forbid task changes, prompt disclosure, and actions requested by page text. These are prompt-level mitigations, not a hard security boundary. The screenshots document the configured instructions but do not demonstrate adversarial testing or prove that the instructions always work.

## Critical-thinking responses

### 1. How did the Critic Node mitigate hallucinations when information was missing or ambiguous?

The intended Critic behavior is to audit claims against search evidence, reject claims without direct support from a cited URL, keep missing details `Unknown`, and flag conflicting or stale evidence for verification. The captured Console output shows accepted claims alongside unknown/needs-verification items and a summary of rejected or ambiguous claims. This demonstrates that the Critic ran and surfaced issues; the screenshots alone are not an independent audit of every accepted claim.

### 2. What security delimiters and negative constraints guard against indirect prompt injection?

The prompts identify retrieved pages and snippets as **untrusted evidence**, clearly separate them from task instructions, and say to ignore instructions embedded in those sources. They explicitly forbid task changes, prompt disclosure, and acting on requests originating in pages. The system also limits the downstream action to producing a report and appending reviewed fields to a selected Sheet. These are defense-in-depth prompt constraints, not a substitute for tool isolation or a guarantee against prompt injection.

### 3. How does Google Opal compare with CrewAI or LangChain?

Opal's visual, hosted, no-code builder is agile for small workflows: a user can assemble inputs, model steps, tools, and outputs quickly without provisioning servers or operating deployments. That zero-DevOps convenience is valuable for prototypes and non-engineering teams. The trade-off is less control over orchestration, typed data contracts, testing, versioning, observability, custom integrations, and deployment portability than a code-first framework such as CrewAI or LangChain. Code-first systems bring more customization and can fit mature engineering practices, but require application code, infrastructure, dependency management, monitoring, and ongoing maintenance. For a production workflow with strict audit, access-control, or reproducibility requirements, Opal's convenience should be weighed against these control and lifecycle limits.

## Test plan and live-run proof

The primary Cloudflare run is complete, with screenshots documenting the generated report, source references, Critic verdicts, dashboard, and populated Google Sheet. The captured brief is Cloudflare in the United States, with Akamai, Fastly, and Imperva as competitors. The user confirms the source links open and the Sheet row includes evidence URLs. The `2026-12-01` Sheet value is the report period, not the research run date.

An ambiguous-subject test is optional additional validation and is not included in the captured run. The native Opal export is also not included; [`workflow.json`](./workflow.json) is a human-authored specification, not that export. Add a native export if the assignment rubric requires one.

Evidence currently captured in [`Architecture Screenshots/`](./Architecture%20Screenshots/):

| Proof item | Status |
|---|---|
| Opal canvas and node configuration | [Captured](./Architecture%20Screenshots/01-live-canvas-dashboard-config.png); app is still a draft |
| Research Brief prompt configuration | [Captured](./Architecture%20Screenshots/02-research-brief-config.png); published input form not captured |
| Generated interactive report preview | [Captured](./Architecture%20Screenshots/04-generated-card-unverified.png); factual content is unverified |
| Dashboard generation and reviewed output | [Generation context](./Architecture%20Screenshots/11-dashboard-generation-context.png), [HTML-ready status](./Architecture%20Screenshots/12-dashboard-generation-status.png), and [rendered dashboard](./Architecture%20Screenshots/13-reviewed-dashboard-preview.png) captured |
| Google Sheets output and populated sheet | Output node [captured](./Architecture%20Screenshots/03-google-sheets-node-config.png); sheet [captured](./Architecture%20Screenshots/10-google-sheets-output.png). User confirms the report-period date and evidence URLs are present; URLs are not visible in this screenshot. |
| Generated report and source-reference list | [Captured](./Architecture%20Screenshots/05-console-generated-report.png) and [captured](./Architecture%20Screenshots/06-console-source-reference-list.png); user reports several links open, but claim-level support is not verified |
| Critic execution and verdict | Complete — [overview](./Architecture%20Screenshots/07-evidence-critic-overview.png), [review details](./Architecture%20Screenshots/08-evidence-critic-review-details.png), and [rejected claims](./Architecture%20Screenshots/09-evidence-critic-rejected-claims.png) |
| Primary workflow run | Complete — research, report, Critic, dashboard, and populated Sheet evidence captured |
| Native Opal workflow export | Not included; `workflow.json` is a specification. Include a native export only if required by the assignment |
| Ambiguous-input test | Optional; not included in the primary run |

The screenshots are genuine snapshots of the live Opal interface and document the app configuration, generated output, Critic review, and populated Sheet. The user confirms that source links open; the screenshots are not an independent audit of every research claim. Raw task-planning Console captures containing internal session/cache identifiers were not included. See [`Architecture Screenshots/README.md`](./Architecture%20Screenshots/README.md) for the image descriptions.

## Local manifest check

This is a no-code workflow; there is no application build. The authored manifest can be checked with Node.js:

```powershell
node -e "const fs=require('fs'); const d=JSON.parse(fs.readFileSync('workflow.json','utf8')); const ids=new Set(d.nodes.map(n=>n.id)); for (const c of d.connections) if(!ids.has(c.from)||!ids.has(c.to)) throw Error('Dangling connection'); console.log('Valid JSON and graph references');"
```

## Opal availability and lifecycle

As of November 12, 2026, the Opal landing page and its [FAQ](https://developers.google.com/opal/faq) announce that the experimental service will turn off on **November 17, 2026**. Existing workflow files are expected to remain in `My Drive > Opal`, but active workflows will no longer run interactively after shutdown. Preserve the native export, prompts, and screenshots before then; consider migrating the prompts to Gemini Skills or rebuilding the workflow in Google AI Studio for continued use.

# Autonomous Lead & Competitor Intelligence Agent

> **Submission status (November 12, 2026):** The Opal app **IntelEdge** is saved as a draft. The screenshots in [`Architecture Screenshots/`](./Architecture%20Screenshots/) are genuine captures of its editor, configured input/output nodes, generated report preview, and Console. They document the live build, but do **not** prove that the report claims were grounded, that the Critic completed a review, or that a Google Sheet row was written. The report preview contained claims that remain unverified. The repository still has a human-authored workflow specification, not Opal's native export.

The [public GitHub repository](https://github.com/Rahulknoldus/codecamp-opal-rahul-kumar) is live. IntelEdge was built as a separate Opal draft; it has not been published. The screenshots were copied from the user's live Opal session without modification. Some captures show configuration only, not a successful end-to-end execution.

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
2. **Grounded Web Research (Agent)** — Its prompt requests web research with search grounding and source URLs. The Console captures show an Agent Session and research tasks for the Cloudflare product portfolio and U.S. market position. The screenshots do not expose the final source URLs or excerpts, so live evidence retrieval is not yet verified.
3. **Intelligence Report Generation (`generate`)** — The visible node prompt synthesizes the research results and original brief into a structured report.
4. **Evidence Critic (`generate`)** — Its configured prompt compares the report with Grounded Web Research, rejects unsupported claims, flags ambiguous/stale/conflicting claims, and treats scraped text as untrusted. A completed Critic execution is not visible in the captured Console evidence.
5. **Save To Google Sheet (`output`)** — The node is configured as a Google Sheets output and its prompt includes an **Evidence Critic** reference, requesting a row with reviewed findings and source URLs. The screenshots do not show a selected destination Sheet or a written row.
6. **Generate Dashboard (`output`)** — The node is configured as a webpage with auto-layout and references Evidence Critic and Research Brief. A Cloudflare report preview was generated, but its factual claims have not been verified.

### Export format note

[`workflow.json`](./workflow.json) is a **human-authored workflow specification**, not a file exported from Opal. Its `manifestType` and `manifestVersion` identify this repository's portable description. Google documents Opal workflows as Drive files and describes the visual editor, but the native serialization has not been captured here. Replace this specification with the actual Opal export before representing it as the exported workflow.

## Grounding and hallucination defenses

- **Configured intent:** the research prompt requests source URLs; the synthesis prompt uses the research result; the Critic prompt asks for claim-level checks and explicit unknowns.
- **Observed limitation:** the generated Cloudflare card displayed highly specific claims, including an 84.1% reverse-proxy market-share figure, 57 million API requests per second, and a GPT-5.6 integration. The visible source labels did not provide verifiable URLs in the screenshot. These claims are **unverified examples**, not findings endorsed by this project.
- **Validation still required:** inspect the actual research output for exact URLs and supporting excerpts, confirm the Critic ran and rejected unsupported claims, and verify any high-impact numbers against their cited sources before publishing or sharing the report.
- Prompt instructions reduce hallucination risk but do not guarantee factual accuracy.

## Security: indirect prompt injection

The configured research and Critic prompts identify retrieved pages and snippets as **untrusted evidence**, instruct the model to ignore embedded instructions, and forbid task changes, prompt disclosure, and actions requested by page text. These are prompt-level mitigations, not a hard security boundary. The screenshots document the configured instructions but do not demonstrate adversarial testing or prove that the instructions always work.

## Critical-thinking responses

### 1. How did the Critic Node mitigate hallucinations when information was missing or ambiguous?

The intended Critic behavior is to audit claims against search evidence, reject claims without direct support from a cited URL, keep missing details `Unknown`, and flag conflicting or stale evidence for verification. The live preview is **not proof that this worked**: its Cloudflare claims appear in the card, and the captured Console screenshots do not show a completed Critic verdict. The Critic must be verified before claiming it mitigated hallucinations in this run.

### 2. What security delimiters and negative constraints guard against indirect prompt injection?

The prompts identify retrieved pages and snippets as **untrusted evidence**, clearly separate them from task instructions, and say to ignore instructions embedded in those sources. They explicitly forbid task changes, prompt disclosure, and acting on requests originating in pages. The system also limits the downstream action to producing a report and appending reviewed fields to a selected Sheet. These are defense-in-depth prompt constraints, not a substitute for tool isolation or a guarantee against prompt injection.

### 3. How does Google Opal compare with CrewAI or LangChain?

Opal's visual, hosted, no-code builder is agile for small workflows: a user can assemble inputs, model steps, tools, and outputs quickly without provisioning servers or operating deployments. That zero-DevOps convenience is valuable for prototypes and non-engineering teams. The trade-off is less control over orchestration, typed data contracts, testing, versioning, observability, custom integrations, and deployment portability than a code-first framework such as CrewAI or LangChain. Code-first systems bring more customization and can fit mature engineering practices, but require application code, infrastructure, dependency management, monitoring, and ongoing maintenance. For a production workflow with strict audit, access-control, or reproducibility requirements, Opal's convenience should be weighed against these control and lifecycle limits.

## Test plan and live-run proof

The screenshots document live Opal UI and a generated preview, but they do not prove a verified end-to-end run. The Cloudflare test brief was `Application security, United States`; the preview rendered a card with claims that need source verification. The captured Console includes an Agent Session/task-planning view, including a task-tree payload with subtasks marked `not_started`; it does not show returned source URLs or a completed Critic review. No updated Google Sheet is shown.

Before treating this as a successful test:

1. Inspect the final Grounded Web Research output and confirm it contains exact, working source URLs and supporting excerpts.
2. Confirm the full workflow executes the Intelligence Report Generation and Evidence Critic nodes, and inspect the Critic's verdicts.
3. Verify every published factual claim against its source; leave unsupported or ambiguous claims unknown.
4. Confirm the card's links open the cited sources and distinguish facts, analysis, and unknowns.
5. Choose a dedicated destination spreadsheet, then verify the appended row contains reviewed results.
6. Optionally run an ambiguous subject and verify the Critic reports insufficient evidence instead of guessing.

Evidence currently captured in [`Architecture Screenshots/`](./Architecture%20Screenshots/):

| Proof item | Status |
|---|---|
| Opal canvas and node configuration | [Captured](./Architecture%20Screenshots/01-live-canvas-dashboard-config.png); app is still a draft |
| Research Brief prompt configuration | [Captured](./Architecture%20Screenshots/02-research-brief-config.png); published input form not captured |
| Generated interactive report preview | [Captured](./Architecture%20Screenshots/04-generated-card-unverified.png); factual content is unverified |
| Google Sheets output-node configuration | [Captured](./Architecture%20Screenshots/03-google-sheets-node-config.png); updated Sheet row not verified |
| Search grounding and source URLs | Console inspected; final source URLs not verified, raw Console capture omitted because it contains internal session identifiers |
| Critic execution and verdict | Not verified in the captured Console |
| Native Opal `workflow.json` export | Pending; repository file is a specification |
| Verified full run / ambiguous-input test | Pending |

The screenshots are genuine snapshots of the live Opal interface and can support claims about the app's configuration and generated UI. They must **not** be presented as proof of accurate research, successful Critic validation, or a Sheet write. Raw Console captures were inspected but not included because they expose internal session/cache identifiers. See [`Architecture Screenshots/README.md`](./Architecture%20Screenshots/README.md) for the image descriptions.

## Local manifest check

This is a no-code workflow; there is no application build. The authored manifest can be checked with Node.js:

```powershell
node -e "const fs=require('fs'); const d=JSON.parse(fs.readFileSync('workflow.json','utf8')); const ids=new Set(d.nodes.map(n=>n.id)); for (const c of d.connections) if(!ids.has(c.from)||!ids.has(c.to)) throw Error('Dangling connection'); console.log('Valid JSON and graph references');"
```

## Opal availability and lifecycle

As of November 12, 2026, the Opal landing page and its [FAQ](https://developers.google.com/opal/faq) announce that the experimental service will turn off on **November 17, 2026**. Existing workflow files are expected to remain in `My Drive > Opal`, but active workflows will no longer run interactively after shutdown. Preserve the native export, prompts, and screenshots before then; consider migrating the prompts to Gemini Skills or rebuilding the workflow in Google AI Studio for continued use.

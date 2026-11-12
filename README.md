# Autonomous Lead & Competitor Intelligence Agent

> **Submission status:** This repository contains the complete workflow design and prompt specification, but not yet a verified native Opal export or live execution proof. Those must be captured from the signed-in Opal app and the connected Google Sheet; this README does not claim they have happened.

The supplied Opal URL currently opens a published, empty app (`codeCampassignment`). Remixing it could not create a Drive copy because this session blocks Opal's Drive `files/generateIds` endpoint. The app URL is retained in the manifest for reference; no changes to the published source app were made. No public GitHub repository has been created from this incomplete package.

## Objective

Build a multi-step, grounded research assistant in Google Opal. Given a company, product, or technology, it searches the live web, returns concise and source-linked market intelligence, subjects the synthesis to a separate evidence critic, and presents reviewed results in an interactive report and an append-only Google Sheets log.

## Business problem

Business development and product teams need a quick, repeatable view of an unfamiliar vendor or technology: what it does, who it serves, how it is positioned, who it competes with, and what has changed recently. Manual research is slow and inconsistent; unverified AI summaries can misstate prices, dates, or product capabilities. This workflow addresses that problem by retaining source links with claims, separating analysis from fact, explicitly reporting unknowns, and inserting a critic between synthesis and delivery.

## Architecture

The graph is specified in [`workflow.json`](./workflow.json). The intended topology is:

```text
Research Brief ──> Grounded Web Research ──> Structured Intelligence ──> Evidence Critic
       └───────────────────────────────────────────────┘                    ├──> Google Sheets row
                                                                            └──> Interactive report
```

### Node-by-node technical breakdown

1. **Research Brief (`user_input`)** — Collects the required subject and optional market/geography and known competitors. The subject is required so the workflow does not silently research an empty query.
2. **Grounded Web Research (`generate`, `@Search`)** — Searches current web sources for company/product facts, business model or pricing when public, recent developments, and relevant competitors. It asks for a source title, URL, date, access date, and supporting excerpt for each fact, favors primary sources for company claims, and records coverage gaps.
3. **Structured Intelligence (`generate`)** — Converts the search evidence into a consistent JSON-shaped report: summary, categorized findings, competitor comparisons, opportunities, risks, and unknowns. Each finding retains source URLs; interpretation is labeled separately from fact.
4. **Evidence Critic (`generate`)** — Independently compares each synthesized claim to the collected evidence. It rejects unsupported claims, flags stale/conflicting/ambiguous evidence, and requires unknowns to stay unknown. Both the reviewer verdicts and rejected claims are kept for auditability.
5. **Append Intelligence Log (`output`)** — Configures an Opal Google Sheets output to append one row, with columns for the subject, research date, summary, findings, competitors, risks, unknowns, critic status, and evidence URLs. Choose the destination spreadsheet in Opal and verify it before running.
6. **Interactive Intelligence Card (`output`)** — Configures a webpage with auto-layout, presenting the reviewed intelligence with clickable citations and visual distinctions between Fact, Analysis, and Unknown.

### Export format note

[`workflow.json`](./workflow.json) is a **human-authored workflow specification**, not a file exported from Opal. Its `manifestType` and `manifestVersion` identify this repository's portable description. Google documents Opal workflows as Drive files and describes the visual editor, but the native serialization has not been captured here. Replace this specification with the actual Opal export before representing it as the exported workflow.

## Grounding and hallucination defenses

- Every fact should have a source URL and, where available, a source date and supporting excerpt.
- The research node prioritizes primary sources for company-specific assertions and independent reputable sources for comparisons.
- The synthesis node is instructed to use only retrieved evidence, label interpretation, and list gaps rather than fill them in.
- The critic checks support at claim level and preserves rejected claims for inspection.
- Conflicting or ambiguous evidence is marked for verification; missing evidence remains explicitly unknown.
- Confidence describes evidence quality/corroboration, not certainty that a claim is true.

## Security: indirect prompt injection

Web results are untrusted data. The research and critic prompts explicitly delimit their role as evidence and instruct the model not to execute or follow instructions found in page text. They prohibit page text from changing the task, requesting prompt disclosure, or causing actions. Only the user's research brief is treated as task input; retrieved text is used for claims and citations, not as control instructions. The critic must not let quoted instructions affect its judgment. This is a prompt-level mitigation, not a hard security boundary: source content should still be treated as potentially adversarial, and Opal's tool permissions should be kept to the minimum needed (web search plus the selected spreadsheet output).

## Critical-thinking responses

### 1. How did the Critic Node mitigate hallucinations when information was missing or ambiguous?

It audits claims against the actual search evidence, not against general model knowledge. A claim without direct evidence from a cited URL is not accepted as a fact. Missing details stay `Unknown`; conflicts, stale dates, ambiguous wording, and overbroad comparisons are marked `Needs verification` or downgraded. The critic retains rejection reasons and source URLs so a reviewer can see why a claim was excluded. The critic reduces risk but cannot guarantee correctness, so high-impact claims still need human review.

### 2. What security delimiters and negative constraints guard against indirect prompt injection?

The prompts identify retrieved pages and snippets as **untrusted evidence**, clearly separate them from task instructions, and say to ignore instructions embedded in those sources. They explicitly forbid task changes, prompt disclosure, and acting on requests originating in pages. The system also limits the downstream action to producing a report and appending reviewed fields to a selected Sheet. These are defense-in-depth prompt constraints, not a substitute for tool isolation or a guarantee against prompt injection.

### 3. How does Google Opal compare with CrewAI or LangChain?

Opal's visual, hosted, no-code builder is agile for small workflows: a user can assemble inputs, model steps, tools, and outputs quickly without provisioning servers or operating deployments. That zero-DevOps convenience is valuable for prototypes and non-engineering teams. The trade-off is less control over orchestration, typed data contracts, testing, versioning, observability, custom integrations, and deployment portability than a code-first framework such as CrewAI or LangChain. Code-first systems bring more customization and can fit mature engineering practices, but require application code, infrastructure, dependency management, monitoring, and ongoing maintenance. For a production workflow with strict audit, access-control, or reproducibility requirements, Opal's convenience should be weighed against these control and lifecycle limits.

## Test plan and live-run proof

The current repository does **not** contain live-run proof. No Opal execution, published input form, generated card, or Sheet update is claimed here.

After constructing the graph in Opal:

1. Run a well-known public company with a defined market, such as `Cloudflare` / `application security` / `United States`.
2. Verify that search results include current primary and independent sources, and that every factual finding links back to a source.
3. Review the critic's verdicts; confirm unsupported claims are rejected and missing data remains unknown.
4. Confirm the output card distinguishes facts, analysis, and unknowns and its links open the cited sources.
5. Confirm exactly one correctly populated row is appended to the intended Sheet.
6. Run an ambiguous or low-information subject and verify that the critic reports insufficient evidence rather than inventing details.

Record the actual run date, input, and results below, then add redacted screenshots under [`Architecture Screenshots/`](./Architecture%20Screenshots/):

| Proof item | Status |
|---|---|
| Opal canvas and node connections | Pending |
| Published input form | Pending |
| Generated interactive output card | Pending |
| Updated Google Sheet row | Pending |
| Normal and ambiguous-input live runs | Pending |

## Architecture Screenshots

See [`Architecture Screenshots/README.md`](./Architecture%20Screenshots/README.md) for the required captures and evidence guidance. Do not include private customer data or account secrets in screenshots.

## Build and verification

This is a no-code Opal workflow specification; there is no application build. Validate the JSON syntax and graph references locally with:

```powershell
python -c "import json; d=json.load(open('workflow.json', encoding='utf-8')); ids={n['id'] for n in d['nodes']}; assert all(c['from'] in ids and c['to'] in ids for c in d['connections']); print(f'{len(ids)} nodes, {len(d[\"connections\"])} connections: valid')"
```

## Opal availability and lifecycle

As of November 12, 2026, the Opal landing page and its [FAQ](https://developers.google.com/opal/faq) announce that the experimental service will turn off on **November 17, 2026**. Existing workflow files are expected to remain in `My Drive > Opal`, but active workflows will no longer run interactively after shutdown. Preserve the native export, prompts, and screenshots before then; consider migrating the prompts to Gemini Skills or rebuilding the workflow in Google AI Studio for continued use.

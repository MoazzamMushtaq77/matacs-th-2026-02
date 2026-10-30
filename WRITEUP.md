# Design Write-up

## 1. AI vs. rules

AI extracts proposal facts into a fixed JSON schema and attaches short evidence quotations. This is where language understanding helps, especially with varied wording and long PDFs. Plain code validates the schema and evidence fields, applies mandatory pass/fail rules, calculates criterion scores and weighted totals, applies tie-breaks, and serializes `ranking.json`. The AI never decides the winner. If extraction is invalid after a bounded retry, or evidence is unclear, the proposal is flagged for human review.

## 2. Consistency and explainability

The category configuration, scoring rubric, prompt version, and model identifier are versioned. Numeric scoring and tie-breaking use deterministic code and unrounded internal values; displayed totals are rounded to two decimals. Ties are resolved by higher experience score, then higher price score, then proposal letter alphabetically. A reviewer can inspect each extracted value, its evidence quote, criterion-level score, formula, weighted contribution, flags, and configuration version. Re-running with the same extracted facts and same configuration produces the same score and ranking; AI extraction itself may vary, so extraction results are validated and retained rather than silently replaced.

## 3. Human in the loop

A reviewer checks unresolved mandatory evidence, material missing or conflicting weighted information, failed extraction validation, and any suspicious content. Reviewers can correct evidence or override a result, but must record their identity and a written reason; the original automated result is preserved. The audit record includes proposal letter, source/page references, extracted values and evidence, prompt/model version, criteria version, scoring calculations, flags, timestamps, reviewer actions, override reason, and final decision. A human makes the final award decision.

## 4. Changing criteria

Criteria are configured through the admin interface, never hard-coded. Editing a criterion or weight creates a new immutable configuration version. The admin explicitly triggers a full-batch rescore so proposals in one comparison use the same criteria version. Prior results and their configuration remain available for audit; the system does not silently mix scores calculated under different versions. The new weights must total 100%, and changes are recorded with who changed them and when.

## 5. Risks and safeguards

- **Wrong extraction or hallucinated facts:** require evidence quotes, schema/type validation, bounded retry, and human review for unresolved fields; never fill gaps by inference.
- **Missing versus negative evidence:** distinguish not stated, conflicting, and explicitly absent; only confirmed mandatory failure yields `ineligible`, while unresolved mandatory evidence yields `flagged`.
- **Prompt injection or manipulation:** treat proposal text as untrusted data, ignore embedded instructions, and log suspicious content without automatically penalizing the bidder.
- **Inconsistent scoring or changing criteria:** calculate scores in deterministic code, version prompts and criteria, preserve audit history, and explicitly rescore the whole batch after a criteria change.
- **Bias and unfair comparisons:** use the same disclosed criteria and scoring rules for all proposals; show evidence and criterion-level contributions; send material gaps for review instead of inventing values.
- **Budget or timeline exceptions:** the brief identifies only two mandatory pass/fail criteria. Treat the $260,000 ceiling and 16-week target as configured constraints; a bid over budget or beyond the timeline is flagged for review and receives the configured criterion treatment, not silently reclassified as a mandatory failure.
- **Cost and operational risk:** use a small extraction prompt, cap retries, log model usage, and keep a human approval step. Do not commit API keys; if a prototype is added, use environment variables and include `.env.example`.

## Scoring assumptions used for this sample

The weighted criteria are experience 30%, price 25%, timeline 15%, warranty 15%, and team/approach 15%. Experience bands are 100 for 15+ qualifying installs, 90 for 12–14, 80 for 9–11, 65 for 6–8, 45 for 3–5, 25 for 1–2, and 0 for none. In-budget price score is the lowest mandatory-eligible in-budget price divided by the proposal price, multiplied by 100. Timeline scores are 100 for 8 weeks or less, 90 for 9–10, 80 for 11–12, 65 for 13–14, and 50 for 15–16; an uncommitted timeline is unknown. Warranty score is the average of panel warranty (up to 25 years) and workmanship warranty (up to 12 years), each capped at 100. Team/approach has four equally weighted elements: named lead, crew, safety plan, and performance monitoring. These are proposed scoring choices because the brief specifies weights but not detailed formulas.

Only proposals with mandatory checks passed and complete material evidence receive a final rank. A and E are ranked from their final scores. B is ineligible on the bidder-licence requirement. C and D are flagged with null scores and ranks because their material evidence is incomplete; no provisional total is presented as a final score.

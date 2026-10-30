# MATACS Associate Software Developer Assessment

- **Candidate:** Moazzam Mushtaq
- **Assessment reference:** MATACS-TH-2026-02
- **Time actually spent:** 50 minutes

## Project

AI Proposal Triage Design for a commercial solar installation project.

## Deliverables

- [Process-flow diagram](diagram/process-flow.png)
- [Assessment write-up](WRITEUP.md)
- [AI extraction prompt](PROMPTS.md)
- [Expected ranking](ranking.json)

## How AI tools were used

I used AI assistance to structure the workflow, draft an evidence-extraction prompt, explore scoring options, and review edge cases. I checked the proposed decisions against the assessment requirements and treated AI-generated suggestions as drafts that needed verification.

One correction was to avoid publishing provisional numeric totals for C and D in `ranking.json`. The required schema has no field to label a score as provisional, and those proposals have material evidence gaps; I therefore set their scores and ranks to `null` until review is complete.

## Design summary

The design separates AI-based evidence extraction from deterministic eligibility checks and score calculation. Uncertain or materially incomplete evidence is sent for human review, and reviewer overrides retain an explanation and the original result. A human makes the final award decision.

This repository documents a proposed design and expected outcomes. It does not claim that a production system or live model evaluation has been completed.

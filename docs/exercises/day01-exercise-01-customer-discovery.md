# Day 1 — Exercise 1: Customer Discovery

**Status:** Completed · **Your answer:** B (correct)

## Customer scenario
An enterprise IT operations customer says:

> “We have 10,000 incidents every month. Our engineers spend too much time investigating them. We want an AI agent that automatically resolves incidents.”

All figures are illustrative training assumptions.

## Question: What would you do first?
- **A.** Start implementing a LangGraph multi-agent workflow.
- **B.** Investigate the current incident process, available data, constraints and success metrics.
- **C.** Select the best LLM and estimate infrastructure cost.
- **D.** Build a ServiceNow integration and demonstrate automated ticket closure.

## Your selection
**B — Correct.**

The customer's **problem** is excessive investigation time; the request for an autonomous agent is a **proposed solution**, not a validated requirement.

## Why B is best
Before designing a system, an FDE should:
1. Map the workflow from incident creation to resolution.
2. Determine where engineers lose time.
3. Verify data availability, quality, access and integration constraints.
4. Identify security requirements and approval boundaries.
5. Establish baseline metrics and agree on measurable outcomes.
6. Scope the smallest useful pilot and get customer feedback.

### Example discovery questions
- Which incident categories consume the most time?
- What are the time breakdowns for classification, knowledge search, investigation and documentation?
- Which tools hold incidents, logs and knowledge articles?
- What data may the solution access or transmit?
- What must remain human-controlled?
- What outcomes will define success after three weeks?

## Illustrative discovery findings
Assume a 20-minute investigation: classification 3 min; knowledge search 8 min; investigation 6 min; documentation 3 min. Knowledge search accounts for 40% of time.

This suggests testing a **read-only, cited knowledge assistant** before attempting automated remediation.

### Example pilot targets (not actual measured results)
| Metric | Baseline | Proposed target |
|---|---:|---:|
| Knowledge search | 8 min | 3 min |
| Total investigation | 20 min | 15 min |
| Retrieval quality | To measure | >=90% on defined evaluation set |
| Engineer acceptance | To measure | >=70% |
| Unauthorized ticket modifications | 0 | 0 |

## Why the other answers are weaker
- **A:** Commits to agents before discovering the bottleneck or risks.
- **C:** Chooses model infrastructure before validating the business case.
- **D:** Assumes automated ticket modification is safe and authorized.

## Key lesson
**Start with customer outcomes and constraints, not technology.** The best MVP may be smaller than the customer's first proposed solution.

## Related notes
- [FDE fundamentals](../day01-fde-fundamentals.md)
- [Exercise 2: MVP architecture](day01-exercise-02-rag-architecture.md)

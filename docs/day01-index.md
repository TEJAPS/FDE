# Day 1 — Learning Index

**Week 1 · Day 1 of 84** — FDE fundamentals, customer discovery, architecture decisions and enterprise security.

## Core concepts
[What is an FDE? Role comparisons, FDE lifecycle and practical customer scenarios](day01-fde-fundamentals.md)

## Exercises — purpose, description and progress

### [Exercise 1 — Customer Discovery and Problem Framing](exercises/day01-exercise-01-customer-discovery.md)
**Purpose:** Practice identifying the business problem and measurable success criteria before selecting technology.

**Description:** An IT operations customer with 10,000 monthly incidents asks for an autonomous AI agent. Decide what an FDE should do first and which discovery questions reveal the actual workflow bottleneck.

**Outcome:** Discovery-first decision, stakeholder questions and pilot KPIs. **Completed — Answer B (correct).**

### [Exercise 2 — MVP Architecture Selection: RAG-Based Incident Assistance](exercises/day01-exercise-02-rag-architecture.md)
**Purpose:** Translate customer requirements into a minimal, safe, defensible technical solution.

**Description:** Design a three-week pilot using ServiceNow tickets, internal runbooks and cited suggestions, while respecting tenant permissions and prohibiting automatic ticket closure. Compare RAG with agents, fine-tuning and rules.

**Outcome:** Architecture, data flow, trade-offs and pilot plan. **Completed — Answer B (correct).**

### [Exercise 3 — Enterprise RAG Data Security and Access Control](exercises/day01-exercise-03-enterprise-rag-data-security.md)
**Purpose:** Learn to enforce privacy and access controls outside the LLM.

**Description:** Plan ingestion of 50,000 historical tickets, 30% with sensitive data, and restricted team documents. Cover PII handling, tenant isolation, document ACLs, authorized pgvector retrieval, model data boundaries and auditing.

**Outcome:** Security design, SQL examples, audit event and negative tests. **Completed — Answer B (correct).**

### Exercise 4 — Security Incident Response and Rollout Decision (next)
**Purpose:** Practice incident containment, transparent customer communication, root-cause analysis and evidence-based release decisions.

**Description:** During a pilot, a Payments engineer receives a restricted Database-team runbook excerpt. Decide what to do immediately and what evidence is needed before restarting the pilot.

**Status:** Not started.

## Additional case-study documentation
- [Detailed customer discovery and RAG MVP architecture](customer-scenarios/day01-incident-rag-mvp.md)

## Documentation conventions
Each exercise uses a numbered filename under `docs/exercises/`, and includes **purpose, description, expected outcome, original question/options, your answer, reasoning, trade-offs and takeaways**. Concept notes live separately. All scenario figures and KPIs are fictional training assumptions unless otherwise stated.

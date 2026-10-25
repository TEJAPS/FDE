# Day 1 — FDE Fundamentals and Learning Notes

> Dpoint FDE: Week 1, Day 1. Conceptual learning notes; the architecture exercise is documented separately in [Incident RAG MVP](customer-scenarios/day01-incident-rag-mvp.md).

## 1. What is a Forward Deployed Engineer (FDE)?

A **Forward Deployed Engineer** is a hands-on software engineer who works closely with customers to understand their real operational problems, scope solutions, integrate software with customer systems, build and deploy working applications, and measure adoption and business results.

The defining characteristic is **end-to-end ownership close to the customer**, not the use of any particular AI framework. FDE roles vary by company: some focus on data and platform integrations, others on AI applications, deployment or complex technical delivery.

**Core mindset:** Start with the customer's problem, not the technology or solution the customer initially requests.

## 2. FDE vs related roles

| Dimension | Software Engineer (SWE) | AI Engineer | Solutions Architect | Forward Deployed Engineer |
|---|---|---|---|---|
| Primary focus | Build and maintain software products/features | Build and evaluate AI capabilities | Design viable technical solutions | Deliver working solutions to specific customer problems |
| Customer interaction | Varies by team | Varies | Typically high | Typically high |
| Production coding | Usually central | Usually central | Varies | Usually central |
| Architecture | Often | Often | Central | Central to delivery |
| Customer-system integration | Sometimes | Often | Usually designs it | Frequently implements it |
| Deployment and troubleshooting | Often | Often | Varies | Often hands-on |
| Success measures | Reliability, features, product outcomes | AI quality and operational metrics | Technical fit and solution outcomes | Customer adoption and measurable business impact |

These are **typical emphases, not rigid job boundaries**.

### Same problem, different perspectives

Customer: “We receive 10,000 incidents/month and investigation takes too long. We want an AI agent that automatically resolves incidents.”

- **SWE:** May implement a specified ticket classification or search API.
- **AI Engineer:** May implement retrieval, model inference, and evaluation.
- **Solutions Architect:** May design the integration, security and deployment architecture.
- **FDE:** First validates the bottleneck and success criteria, then scopes, builds, integrates, deploys, demonstrates and measures a suitable solution.

An FDE can do parts of all three other roles. They should also know when *not* to build autonomous agents.

## 3. FDE delivery lifecycle

1. **Discover:** Interview users, map workflows, identify bottlenecks and constraints.
2. **Define:** Agree on business outcomes, baselines, target metrics, scope, permissions and acceptance criteria.
3. **Prototype:** Build the smallest useful solution; validate it with real users and representative data.
4. **Integrate and deploy:** Connect enterprise systems, implement security, testing, reliability and observability.
5. **Measure and iterate:** Track adoption, quality, latency, costs and customer impact; improve or change direction based on evidence.

Customer feedback occurs **throughout** the lifecycle, not only at the end.

## 4. Discovery before architecture

Ask:
- Where do incidents originate and who owns the workflow?
- Which stage takes the most time: classification, knowledge search, investigation, documentation or approvals?
- What historical tickets, knowledge articles, logs and runbooks are available?
- What are the user, team, tenant and document access rules?
- Which actions may the system read or modify, and which need approval?
- What are the security, privacy, data residency, cost and delivery constraints?
- What baseline and pilot success metrics will be used?

**Business problem vs proposed solution:** “Investigation takes too long” is the problem; “build an autonomous AI agent” is the customer's proposed solution. The latter must be validated rather than accepted uncritically.

## 5. Fictional customer scenario and discovery

Assume 10,000 incidents/month and an average 20-minute investigation, broken down as:
- Classification: 3 minutes
- Knowledge search: 8 minutes
- Investigation: 6 minutes
- Documentation: 3 minutes

This is **illustrative training data, not observed customer evidence**. Since knowledge search represents 40% of the time, a retrieval assistant is a plausible pilot.

Illustrative pilot targets:
- Knowledge search: 8 → 3 minutes
- Total investigation: 20 → 15 minutes
- Retrieval relevance: >=90% on a defined evaluation dataset
- Engineer suggestion acceptance: >=70%
- Unauthorized ticket modifications: 0

The actual targets must be agreed with the customer and tested on representative incidents.

## 6. Architecture decision: why RAG first?

**Selected:** Read-only ServiceNow-style ticket adapter + FastAPI + approved document ingestion + PostgreSQL/pgvector + LLM suggestions with verifiable citations + human review.

Why:
- Addresses the knowledge-search bottleneck.
- Fits a narrow three-week pilot better than autonomous remediation.
- Updates knowledge without retraining.
- Supports cited suggestions and measurable retrieval quality.
- Respects the customer's no-automatic-closure requirement.

Alternatives:
- **Autonomous multi-agent resolution:** too risky and out of scope for the initial pilot.
- **Fine-tuned LLM:** unnecessary before validating retrieval and available data.
- **Rules-only/keyword search:** useful baseline or fallback; may not handle varied phrasing as flexibly.

Do **not** introduce LangGraph, Kafka, Camunda or Redis merely because they are available; justify each with workflow or scale requirements.

## 7. RAG architecture components and data flow

**Offline ingestion:** Approved source documents → parsing/cleaning → chunking → metadata and access labels → embeddings → pgvector.

**Request flow:** Engineer → FastAPI authentication/authorization → read-only ticket lookup → tenant- and document-authorized retrieval → LLM grounded suggestion → source/citation validation → human review and feedback.

Important components:
- **FastAPI:** API contract, validation, authentication and orchestration.
- **ServiceNow adapter:** Read-only incident context, scoped to authorized users.
- **PostgreSQL + pgvector:** Store indexed chunks, vectors and access metadata.
- **LLM:** Generate suggestions grounded in authorized evidence; may abstain if insufficient.
- **Citation validator:** Check references against documents actually retrieved and authorized.
- **Engineer review:** No automated incident closure or other write action in the MVP.
- **Observability/evaluation:** Traces, logs, latency, quality and pilot feedback.

## 8. Trade-offs, security and scale

| Choice | Benefit | Limitation |
|---|---|---|
| FastAPI | Rapid Python development | Needs disciplined testing and production operations |
| PostgreSQL + pgvector | SQL, metadata and vector retrieval together | Specialized search systems may be preferable at larger scale |
| RAG | Current, replaceable knowledge | Retrieval quality can limit answer quality |
| Read-only adapter | Lower operational risk | Cannot remediate automatically |
| Human approval | Human accountability | Some manual work remains |
| Single orchestrator | Simpler MVP | May require decomposition later |

Security is enforced **outside the prompt**: verify identity, apply tenant and document authorization in retrieval, use least-privilege credentials, treat ticket/document content as untrusted, protect secrets and PII, validate citations and audit safely.

Scalability: 10,000 incidents/month averages roughly 0.0039 incidents/second, **not** the actual peak AI request rate. Measure user concurrency, questions per incident and incident spikes before capacity planning. Begin with stateless API workers, bounded LLM concurrency, timeouts and indexed queries.

## 9. Three-week pilot delivery outline

- **Week 1:** Confirm data and permissions, define evaluation data, implement read-only adapter and ingestion.
- **Week 2:** Build authorized retrieval, citation generation/validation and evaluation tests.
- **Week 3:** Pilot with support engineers, compare against baseline, fix issues and present results.

## 10. Dpoint tutorial plan

**YouTube video #1:** *Forward Deployed Engineer Explained | FDE vs Software Engineer vs AI Engineer | Real-World Example*

Suggested segments: introduction; FDE responsibilities; role comparison; incident-management customer scenario; FDE lifecycle; MVP architecture and series preview.

**Instagram Reels:** (1) What does an FDE do? (2) FDE vs AI Engineer. (3) Why customer discovery comes before coding.

Only present fictional numbers as examples; do not imply the capstone has been deployed or tested yet.

## 11. Day 1 takeaways

1. FDE is a customer-facing **engineering delivery role**, not a framework.
2. Distinguish the **business problem** from the customer's proposed solution.
3. Choose the smallest safe MVP that can demonstrate measurable value.
4. Defend technical decisions with trade-offs, permissions, cost and timeline.
5. Keep humans in control of high-impact actions until explicitly approved and validated.
6. Measure success in customer outcomes, not just a working demo.

**Next exercise:** Enterprise data security: 50,000 historical tickets, 30% containing sensitive information, and team-restricted documents. Choose an ingestion, authorization and model-data handling approach before implementation.

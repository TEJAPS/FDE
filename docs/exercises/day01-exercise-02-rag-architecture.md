# Day 1 — Exercise 2: RAG-Based Incident Assistance Architecture

**Status:** Completed · **Your answer:** B (correct)

## Customer requirements
- 10,000 incidents per month (fictional scenario).
- Historical incidents in ServiceNow.
- PDFs and internal knowledge articles.
- Suggestions with source citations.
- Tenant and document access restrictions.
- No automatic incident closure.
- First pilot in three weeks.

## Question: Which architecture should we choose?
- **A.** Autonomous multi-agent resolution with automatic ticket updates.
- **B.** FastAPI + read-only ServiceNow adapter + document ingestion + pgvector + LLM-generated cited suggestions + engineer review.
- **C.** Fine-tune and deploy a custom LLM on historical incidents.
- **D.** Rules-only classification and lookup.

## Your selection
**B — Correct.**

## Why B is best
RAG directly addresses knowledge search, enables cited evidence, permits updates to the knowledge base without model retraining, and preserves human control. It is appropriately scoped for a three-week pilot.

A rules/keyword search baseline should still be evaluated against RAG.

## Logical architecture
```text
Support Engineer / REST Client
            |
FastAPI: JWT, RBAC, tenant context, validation
            |
Incident Assistance Service
        /            \
Read-only ticket    Authorized RAG retrieval
adapter             PostgreSQL + pgvector
        \            /
         Context assembly
               |
       LLM grounded answer
               |
      Citation verification
               |
      Human review + feedback

Separate ingestion pipeline:
Approved PDFs/tickets -> parse/clean -> redact if required
-> chunk + security metadata -> embed -> pgvector
```

## Request data flow
1. Authenticate the engineer and derive server-side tenant and permissions.
2. Fetch authorized incident details from the read-only adapter.
3. Retrieve only authorized document chunks with tenant/ACL filters.
4. Send approved, minimized context to the model.
5. Generate troubleshooting suggestions grounded in retrieved evidence.
6. Verify source IDs and citation permissions.
7. Display suggestions to the engineer; do not modify tickets.
8. Capture permitted feedback and pilot metrics.

## Technology trade-offs
| Choice | Why | Trade-off |
|---|---|---|
| FastAPI | Fast Python API iteration | Requires disciplined validation, testing and ops |
| PostgreSQL + pgvector | SQL metadata and vector search together | Specialized search may help at larger scale |
| RAG | Current knowledge and citations | Depends on retrieval quality |
| Read-only ServiceNow adapter | Reduced operational risk | No automated remediation |
| Human review | Control and accountability | Does not remove all manual work |
| Simple orchestration | Fits narrow MVP | Complex workflows may later need LangGraph/Camunda |

## Why not the other options?
- **A:** Excessive scope/risk and conflicts with no-auto-close requirement.
- **C:** Training cost and data preparation without evidence that retrieval is insufficient.
- **D:** Useful baseline, but may struggle with varied incident descriptions and document semantics.

## Security and scalability
- Enforce tenant and document access **before** passing text to the LLM.
- Use least-privilege credentials, sensitive-data handling and safe audit logs.
- Treat documents and incident text as untrusted.
- Handle LLM failures with bounded timeouts and controlled fallbacks.
- 10,000 incidents/month is ~0.0039 incidents/sec on average, **not** the peak AI request rate; measure concurrent users and queries per incident.

## Three-week pilot
1. Week 1: data/permissions, baseline, read-only API, ingestion.
2. Week 2: authorized retrieval, cited answers, evaluation and security tests.
3. Week 3: engineer pilot, measure quality and time saved, demonstrate results.

## Key lesson
**Choose the smallest architecture that solves a validated customer problem safely and measurably.** Add agents, queues and other infrastructure only when justified.

## Related notes
- [Exercise 1: Discovery](day01-exercise-01-customer-discovery.md)
- [Detailed architecture notes](../customer-scenarios/day01-incident-rag-mvp.md)
- [Exercise 3: Enterprise data security](../customer-scenarios/day01-enterprise-rag-security.md)

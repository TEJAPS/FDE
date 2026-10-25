# Day 1 — FDE Customer Discovery and MVP Architecture

Status: Learning exercise completed through architecture selection; no application implementation yet.

## Customer problem (fictional)
10,000 incidents/month. Engineers spend too long investigating incidents. The customer initially requests autonomous resolution, but discovery identifies knowledge search as a major bottleneck.

### Illustrative baseline and pilot targets
| Metric | Baseline | Pilot target |
|---|---:|---:|
| Average investigation time | 20 min | 15 min |
| Knowledge search time | 8 min | 3 min |
| Retrieval relevance | Not yet measured | >=90% on defined evaluation set |
| Engineer acceptance | Not yet measured | >=70% |
| Unauthorized ticket changes | 0 | 0 |

These are **hypothetical assumptions and targets**, not measured outcomes.

## Discovery questions
1. Which incident types and stages consume the most engineer time?
2. What are the incident sources and ServiceNow permissions?
3. Are historical tickets, runbooks and PDFs available and authorized for use?
4. What tenant and document-level access policies apply?
5. What data can be sent to model providers?
6. How will the pilot measure time saved, quality, and engineer adoption?

## MVP decision: RAG-based incident assistance
Chosen over autonomous multi-agent resolution, model fine-tuning, and a rules-only system. A rules-based or keyword search baseline should still be evaluated.

- Read-only ServiceNow-style adapter retrieves permitted ticket context.
- Approved documents and historical incidents are ingested offline, chunked, embedded and indexed in PostgreSQL + pgvector.
- FastAPI authenticates users, checks tenant/document permissions, and orchestrates retrieval.
- An LLM generates grounded troubleshooting suggestions with citations.
- The application validates source references and displays suggestions for human review.
- No automatic incident closure or other write operations in the MVP.

### Logical data flow
```text
Engineer -> FastAPI (JWT/RBAC/tenant checks)
         -> Incident Assistance Service
            |-> Read-only ticket adapter
            |-> Authorized RAG retrieval -> PostgreSQL/pgvector
         -> LLM grounded generation
         -> Citation/source validation
         -> Engineer review + feedback

Approved docs -> ingestion/cleaning/chunking -> embeddings -> pgvector
```

### Engineering trade-offs
- FastAPI for quick API iteration; production standards still required.
- PostgreSQL + pgvector for relational metadata and vector search in one store.
- RAG for updateable knowledge without fine-tuning; retrieval quality must be measured.
- Read-only tools reduce operational risk but cannot perform remediation.
- Human review preserves control but retains manual work.
- Avoid adding LangGraph, Kafka, Camunda or Redis until requirements justify them.

### Security and reliability
- Enforce tenant and document-level authorization in the data access layer.
- Treat ticket content and retrieved documents as untrusted.
- Use least-privilege read-only integration credentials, secret storage and redacted audit logs.
- Validate citations against actually retrieved authorized records.
- Rate-limit and cap LLM concurrency; implement timeouts and controlled fallback.
- Load-test peak AI request traffic; 10,000 monthly incidents alone does not establish request concurrency.

## Three-week pilot outline
1. **Week 1:** Confirm access/data, build read-only ticket API and document ingestion, define baseline and evaluation dataset.
2. **Week 2:** Implement retrieval, citations, suggestion generation and access controls; test quality.
3. **Week 3:** Pilot with engineers, measure search time/acceptance, fix failures and present results.

## Next exercise
Customer now says: “We can share 50,000 historical tickets, but 30% contain sensitive customer information and some documents are restricted by team.” Decide how to handle ingestion, access controls and model data boundaries **before** coding.

# Day 1 — Exercise 3: Enterprise Data Security for RAG

**Status:** Completed. **Exercise answer:** B — controlled ingestion, authorization, and approved model data handling.

## Scenario

A fictional customer has 50,000 historical incident tickets; 30% (15,000) contain sensitive information. Some knowledge articles are restricted by team. The system must not reveal documents to unauthorized engineers. All values are training assumptions.

## Core principles

1. **Enforce access outside the LLM.** System prompts are not an authorization mechanism.
2. **Data minimization.** Only ingest and send information that is needed and approved.
3. **Least privilege.** Read-only integration accounts for the MVP; narrow API and database permissions.
4. **Defense in depth.** Authentication, application checks, SQL authorization, optional PostgreSQL Row-Level Security, audit and testing.
5. **Human review.** The MVP suggests actions but cannot close or update incidents.

## 1. PII handling

PII includes names, emails, phone numbers, account identifiers and combinations of data that identify people. Other sensitive material includes credentials, tokens, financial data and confidential operational details.

**Illustrative input:** `Customer Ravi Kumar (ravi@example.com) reported payment API HTTP 503 on account ACC-48291 after deployment.`

**Sanitized text:** `Customer [PERSON] ([EMAIL]) reported payment API HTTP 503 on account [ACCOUNT_ID] after deployment.`

Ingestion sequence:

```text
Approved source -> classify sensitivity -> redact, tokenize or exclude
-> attach tenant/team/document ACL metadata -> chunk -> embed
-> store encrypted/indexed chunks with retention rules
```

- Sanitize before embedding when policy requires it: embeddings are not automatically privacy-safe.
- Use automated detectors plus representative tests; apply manual review to high-risk records.
- Ensure raw source files, staging data, backups and logs follow the same retention/security policy.
- Preserve only the technical signals necessary for retrieval.

## 2. Tenant isolation vs document authorization

**Tenant isolation:** Bank A cannot access Bank B's data.

**Document-level RBAC/ACL:** Within Bank A, Payments staff can access authorized Payments documents but not restricted Database-team documents unless granted permission.

Effective authorization may depend on tenant, team, explicit user permissions, classification, incident assignment and document status. RBAC may need ABAC or ACL rules.

Never trust a tenant or role identifier supplied by a client. Derive the effective security context from authenticated identity and server-side authorization.

## 3. PostgreSQL + pgvector retrieval authorization

Illustrative schema:

```sql
CREATE TABLE document_chunks (
  id UUID PRIMARY KEY,
  tenant_id UUID NOT NULL,
  document_id UUID NOT NULL,
  content TEXT NOT NULL,
  embedding vector(1536),
  allowed_team_ids UUID[] NOT NULL,
  classification TEXT NOT NULL
);
```

Vector dimension **must match the selected embedding model**.

Illustrative query (simplified for teaching):

```sql
SELECT document_id, content, embedding <=> :query_embedding AS distance
FROM document_chunks
WHERE tenant_id = :authorized_tenant_id
  AND allowed_team_ids && :authorized_team_ids
  AND classification IN ('PUBLIC', 'INTERNAL')
ORDER BY embedding <=> :query_embedding
LIMIT 5;
```

Here `&&` checks array overlap. The authorized IDs must be derived by the server, not accepted from request JSON.

**Production caveats:**
- Define semantics for tenant-wide documents, explicit deny rules, user ACLs and classification.
- Enforce authorization **before** sending content to the LLM.
- Consider PostgreSQL RLS as an additional boundary. Use a non-bypass database role and transaction-scoped, trusted context; test pooled-connection isolation.
- Keep document permissions current, especially after revocation. A stale vector index must not grant access.
- Validate generated citations against retrieved, authorized source records.

## 4. Model data boundaries

```text
Customer-approved data environment
   -> permitted retrieval + PII minimization
   -> model gateway / data policy
   -> approved model deployment
   -> validated output + human review
```

Before using OpenAI, Bedrock or another provider, verify the **specific deployment's** data retention, data-use terms, region/residency, security controls, contractual permissions and incident response obligations. Do not assume that cloud hosting or private networking alone guarantees compliance.

Treat incident text and retrieved documents as **untrusted instructions**. A document saying “ignore previous instructions” is data, not an instruction to the application.

## 5. Audit logging

Useful metadata: request ID, actor, tenant, incident, authorized retrieved document IDs, model/application version, latency, access decision, and user feedback.

Example:

```json
{
  "event": "rag_assistance_completed",
  "request_id": "req-7b21",
  "actor_id": "user-142",
  "tenant_id": "tenant-A",
  "incident_id": "INC0012345",
  "retrieved_document_ids": ["RUNBOOK-042"],
  "model_version": "approved-model-v1",
  "latency_ms": 1320,
  "outcome": "suggestion_returned"
}
```

This is fictional. Do not log raw tickets, full prompts, secrets or generated responses by default. Apply access controls and retention to metadata as well.

## 6. Negative security tests

| Test | Expected result |
|---|---|
| Bank A user requests Bank B ticket | Deny |
| Payments user searches restricted Database article | Exclude |
| Client changes tenant_id in payload | No privilege escalation |
| Retrieved document contains prompt injection | Ignore as instructions |
| Document ACL is revoked | Future retrieval cannot disclose it |
| LLM times out | Controlled error or authorized search-only fallback |
| Sensitive data appears in source | Redact, exclude or handle under approved policy |

## 7. Why the alternatives are weaker

- **A: Ingest everything and rely on prompts.** Prompts cannot enforce confidentiality.
- **C: Per-engineer vector database.** High operational cost, duplication and difficult permission synchronization; not generally necessary.
- **D: Public documents only.** Avoids much of the customer problem and discards valuable approved knowledge.
- **B: Controlled ingestion and authorization.** Provides useful enterprise retrieval with explicit security boundaries.

## 8. FDE customer conversation

Explain the proposal in business terms: “We can build an assistant that searches only documents an engineer is already authorized to see. We'll minimize sensitive data before indexing, keep the integration read-only, validate every cited source, and test cross-tenant and cross-team access before the pilot.”

## 9. Next exercise — Security incident and rollout decision

During pilot testing, a Payments engineer receives a citation to a Database-team-only runbook. The answer includes a short excerpt from that restricted document.

Decide what to do **immediately**, how to investigate the authorization failure, what to communicate to the customer, and what release gate must be met before restarting the pilot.

Related notes: [FDE fundamentals](../day01-fde-fundamentals.md) and [RAG MVP architecture](day01-incident-rag-mvp.md).

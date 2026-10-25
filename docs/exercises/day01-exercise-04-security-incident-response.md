# Day 1 — Exercise 4: Security Incident Response and Rollout Decision

**Status:** Completed · **Your answer:** B (correct) · **Type:** Fictional FDE customer incident simulation

## Purpose
Learn how a Forward Deployed Engineer responds when a customer-facing AI solution breaches a confidentiality boundary: contain the exposure, investigate its scope and root cause, communicate transparently, implement authorization fixes, validate regression tests, and make a defensible go/no-go rollout decision.

## Exercise description
During a controlled pilot of an enterprise RAG incident assistant, a Payments engineer asks about an HTTP 503 payment-service error. The assistant cites **DB-RUNBOOK-047**, a restricted Database-team runbook, and includes a short excerpt. The Payments engineer is not authorized to see that runbook. The customer asks whether the pilot is safe to continue.

## Expected learning outcome / deliverable
A prioritized containment plan, possible root causes, a secure retrieval design, a customer-facing status message, a security regression test matrix, and explicit pilot restart criteria.

## Original question: What should the FDE do first?
- **A — Strengthen the system prompt:** Tell the LLM not to reveal restricted information and continue.
- **B — Contain, investigate and validate:** Disable the affected retrieval capability, preserve evidence, notify the customer security contact, investigate authorization and require regression tests before resuming.
- **C — Delete the restricted document:** Remove DB-RUNBOOK-047 and restart immediately.
- **D — Continue and monitor:** Treat the testing-stage disclosure as low priority.

## Your selection and evaluation
**B — Correct.** This is an actual confidentiality failure within the fictional scenario, not a prompt-quality issue. Customer protection takes precedence over a demo or delivery deadline.

## 1. Immediate containment
1. Disable the affected retrieval/response path; pause the whole pilot if scope is unclear.
2. Preserve request IDs, user/tenant identity, document IDs, relevant access decisions, application versions and narrowly scoped traces securely.
3. Notify the customer's designated security owner through the agreed incident process, stating confirmed facts and unknowns.
4. Determine whether other users, tenants, documents or historical requests were affected.
5. Remediate and require independent security validation and designated customer approval before restart.

Avoid propagating sensitive excerpts in chat, tickets or unprotected logs. Incident notification obligations depend on the customer's policies, contracts and law.

## 2. Illustrative root cause
**Hypothesis, not a confirmed finding:** Global vector similarity search retrieved restricted chunks before access checks; the chunks were sent to the LLM and the response disclosed an excerpt. Filtering citations after generation would not prevent the model from seeing the source.

```python
# INSECURE: authorization is missing
chunks = vector_store.similarity_search(query, k=10)
answer = llm.generate(question=query, context=chunks)
# Filtering citations here is too late.
```

Other hypotheses: stale source ACLs; client-controlled team IDs; DB role bypassing RLS; incorrect pooled-connection identity; response/cache key missing tenant/authorization scope; access recheck missing at citation time.

## 3. Secure retrieval pattern
- Authenticate the user and resolve tenant, team, roles and effective permissions **server-side**.
- Apply tenant and document ACLs during retrieval, before any content reaches the model.
- Add database-level protections such as RLS where appropriate; ensure the application role cannot bypass them and transaction-scoped security context does not leak across pooled connections.
- Synchronize source permissions and revoke access promptly.
- Revalidate source IDs and permissions when assembling the final answer and citations.
- Fail closed if identity or authorization cannot be verified.

```sql
-- Simplified teaching example, not a complete enterprise ACL policy.
SELECT document_id, content, embedding <=> :embedding AS distance
FROM document_chunks
WHERE tenant_id = :authorized_tenant_id
  AND allowed_team_ids && :authorized_team_ids
ORDER BY embedding <=> :embedding
LIMIT 5;
```

The SQL placeholders must be populated from trusted server-side authorization context. Production systems need rules for tenant-wide access, explicit denies, classifications and document revocation. Vector indexes, filters and RLS must be tested under the actual deployment configuration.

## 4. Customer communication example
> We identified an access-control issue during pilot testing in which the assistant displayed an excerpt from a restricted runbook to an unauthorized user. We have disabled the affected retrieval capability and initiated an investigation. We are reviewing the access-control path and audit records to determine the cause and scope. We will share findings and corrective actions with your security team, and we will not resume the affected capability until remediation passes regression testing and designated stakeholders approve the restart. We will provide the next update at the agreed time.

Do not imply the issue is fully understood or resolved before evidence supports that claim.

## 5. Regression tests
| Test | Required outcome |
|---|---|
| Payments user asks about Database-only runbook | No restricted content or citation |
| Tenant A requests Tenant B document | Denied |
| Client tampers with tenant/team identifiers | No privilege escalation |
| Document ACL revoked after ingestion | Cannot retrieve revoked content |
| Unauthorized document has highest semantic similarity | Still excluded |
| Cross-user/cross-tenant cache hit attempted | No leakage |
| DB connection reused after a different user's request | No authorization context leakage |
| Citation points to unauthorized source | Rejected |
| Authorization service unavailable | Fail closed |
| Authorized user requests permitted document | Relevant content returned |

Run tests at API, database and end-to-end levels, including the production-like configuration.

## 6. Go / no-go release criteria
**GO only if:** exposure contained; scope assessed; root cause corrected or an equivalent effective control validated; critical tests pass; ACL/index/cache/log paths checked; required security and delivery owners approve.

**NO-GO if:** unauthorized access remains possible, exposure scope cannot be adequately bounded, critical tests fail, remediation is unverified or required approval is absent.

## Why the alternatives fail
- **A:** Prompts cannot replace authorization.
- **C:** Deleting one runbook treats a symptom, not the systemic exposure path.
- **D:** A disclosure during testing is still a security incident and cannot be dismissed.

## Key FDE takeaways
- Own customer impact, not just the code change.
- Contain first; preserve evidence and communicate without speculation.
- Authorization is a system boundary, not an LLM behavior.
- Prove remediation through negative tests and approval gates.
- Never trade away confidentiality to meet a pilot deadline.

## Day 1 assessment
All four decision exercises completed correctly. **Conceptual Day 1 is complete** (FDE role, discovery, architecture selection, enterprise data security and incident response). This does **not** mean the MVP has been implemented, deployed or security certified. Day 2 will focus on local setup and the first FastAPI health endpoint.

## Related
- [Day 1 learning index](../day01-index.md)
- [Exercise 1 — Customer discovery](day01-exercise-01-customer-discovery.md)
- [Exercise 2 — RAG architecture](day01-exercise-02-rag-architecture.md)
- [Exercise 3 — Enterprise RAG security](day01-exercise-03-enterprise-rag-data-security.md)

# Forward Deployed Engineering (FDE) — 12-Week Master Roadmap

> **Dpoint | Learn → Build → Explain → Publish → Demonstrate**
>
> One synchronized roadmap for an FDE career transition, hands-on enterprise AI engineering, a YouTube FDE playlist, Instagram Reels, and a public GitHub portfolio.

## At a glance

- **Duration:** 12 weeks / 84 calendar days (day-by-day sequence; dates flexible).
- **Learning commitment:** approximately 144–180 hours, **excluding** some recording/editing time.
- **Content targets:** 24 long-form YouTube tutorials (2/week), 36 Instagram Reels (3/week).
- **Portfolio:** one evolving enterprise AI capstone, not 12 disconnected demos.
- **End goal:** explain customer needs, design, implement, deploy, evaluate, operate and present a realistic AI solution; prepare for FDE interviews.
- **Status:** Planned. Check off days as you complete them. The schedule is a target, not a claim that work is already finished.

### Relationship to existing content

The existing [Dpoint Agentic AI playlist](https://www.youtube.com/playlist?list=PLLUFZCmqicc8WVd71bw4xyK7jiugNicVJ) remains separate. This FDE series builds on agent fundamentals, emphasizing enterprise integration, customer discovery, measurable outcomes, production operations and FDE interviews rather than repeating beginner agent tutorials.

## Capstone: Enterprise AI Incident Management Platform

**Customer scenario:** An enterprise IT support team wants to reduce manual incident triage and resolution time while retaining auditability and human control.

**Reference architecture:**

```text
Customer / Support Engineer (Web UI or REST client)
                      |
             FastAPI / Spring Boot
       JWT • validation • tenant isolation
                      |
          AI Workflow Orchestrator
           (LangGraph + policies)
             /       |       \
       Triage    Knowledge    Resolution
        Agent      Lookup     Suggestions
             \       |       /
            Human approval gate
                      |
         Enterprise tools via MCP / REST
            |             |
     Ticket adapter   RAG / pgvector
            |             |
       Audit trail, tracing, evaluation
                      |
      Docker • CI/CD • AWS staging • alerts
```

Build the platform incrementally: API and incident classifier → RAG → evaluations and safety → ticket/MCP integration → agent workflows and approvals → production deployment → performance/ROI → customer demonstration.

Use a **mock ServiceNow-style adapter** unless you have legitimate access to a real instance. Do not commit customer data, API keys, or credentials.

## Technology stack

| Area | Choice |
|---|---|
| Languages | Python 3.12+, Java 21 |
| API frameworks | FastAPI; Spring Boot where useful |
| LLM providers | OpenAI API and/or AWS Bedrock |
| Retrieval | PostgreSQL + pgvector |
| Agent workflows | LangGraph |
| Integrations | REST, webhooks, MCP |
| Long-running business workflows | Camunda 8 |
| Messaging (only if justified) | Kafka |
| Quality | pytest, Ruff, evaluation datasets |
| Containers/cloud | Docker, AWS, GitHub Actions |
| Observability | Structured logging, OpenTelemetry, metrics |
| Development | VS Code, Git, uv; Copilot optional |

**Cost control:** Start locally, set provider budgets, use synthetic datasets, and avoid leaving AWS resources running. A paid Copilot subscription is not required.

## Weekly operating rhythm

| Day within week | Primary activity |
|---|---|
| 1–2 | Learn concepts and document customer/technical requirements |
| 3–4 | Implement features, test and record architecture decisions |
| 5 | Evaluate, harden and prepare tutorial scripts |
| 6 | Finish demos, rehearse and record tutorials / short clips |
| 7 | Publish two YouTube tutorials and three Reels; review the milestone |

Daily technical tasks take priority over publishing volume. Only publish tutorials after the code and demos work.

## Six phases

1. **Weeks 1–2:** Python, FastAPI, LLMs, rapid prototyping.
2. **Weeks 3–4:** Enterprise RAG, evaluation, safety and reliability.
3. **Weeks 5–6:** Customer discovery, enterprise integrations, MCP, agents and Camunda.
4. **Weeks 7–8:** Security, deployment, observability, performance, costs and ROI.
5. **Weeks 9–10:** Coding, SQL and AI system design interviews.
6. **Weeks 11–12:** Customer scenarios, capstone, portfolio and FDE interview readiness.

## Complete 84-day schedule

### Week 1: Python and FDE fundamentals (Days 1–7)

**Weekly outcome:** Set up the development environment and build a customer-facing API.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 1 | FDE vs SWE, AI Engineer and Solutions Architect | Map a customer problem to the FDE lifecycle |
| [ ] 2 | Install Python 3.12+, VS Code, uv, Git, Docker, Ruff and pytest | Create the repository structure and /health API |
| [ ] 3 | Python typing, dataclasses and async/await | Typed request/response models |
| [ ] 4 | FastAPI, Pydantic and error handling | Incident CRUD API |
| [ ] 5 | Authentication, configuration and dependency injection | JWT validation and environment configuration |
| [ ] 6 | Testing and API documentation | pytest suite and OpenAPI docs |
| [ ] 7 | Review, record and publish | YouTube #1 FDE introduction; #2 FastAPI backend; three Reels |

### Week 2: LLMs, tool calling and rapid prototyping (Days 8–14)

**Weekly outcome:** Turn the API into an LLM-powered prototype.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 8 | LLM APIs, tokens, latency, cost and model selection | Integrate an LLM provider |
| [ ] 9 | Structured output and JSON schemas | Extract incident fields from text |
| [ ] 10 | Function calling and tool execution | Safe read-only incident lookup tools |
| [ ] 11 | Prompt injection and untrusted tool output | Tool permissions and validation |
| [ ] 12 | Async calls, timeouts, retries and rate limits | Resilient LLM client |
| [ ] 13 | Rapid prototyping and demos | AI incident classifier MVP |
| [ ] 14 | Review, record and publish | YouTube #3 LLM APIs; #4 AI incident classifier; three Reels |

### Week 3: Enterprise RAG foundations (Days 15–21)

**Weekly outcome:** Build a retrieval system with measurable search quality.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 15 | Embeddings, semantic search and chunking | Document ingestion pipeline |
| [ ] 16 | PostgreSQL and pgvector | Local embedding and metadata store |
| [ ] 17 | Retrieval and metadata filtering | Tenant-aware similarity search |
| [ ] 18 | Grounded RAG responses | /ask API with source citations |
| [ ] 19 | Hybrid search and reranking | Retrieval comparison |
| [ ] 20 | RAG testing and failure analysis | Retrieval evaluation dataset |
| [ ] 21 | Review, record and publish | YouTube #5 RAG architecture; #6 RAG with pgvector; three Reels |

### Week 4: Evaluation, safety and AI reliability (Days 22–28)

**Weekly outcome:** Measure and improve AI quality.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 22 | Evaluation metrics and datasets | Task-success, groundedness and accuracy definitions |
| [ ] 23 | Retrieval evaluation | Recall@k and MRR measurements |
| [ ] 24 | Generation evaluation and human review | Scored evaluation harness |
| [ ] 25 | PII, secrets and prompt injection | Redaction and adversarial tests |
| [ ] 26 | Observability fundamentals | Structured logs, traces and request IDs |
| [ ] 27 | Regression testing and quality gates | Automated evaluation checks |
| [ ] 28 | Review, record and publish | YouTube #7 AI evaluation; #8 RAG security and testing; three Reels |

### Week 5: Customer discovery and enterprise integrations (Days 29–35)

**Weekly outcome:** Translate customer needs into a scoped solution.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 29 | Customer discovery interviews | Stakeholder interview questionnaire |
| [ ] 30 | Problem framing and success metrics | Baseline KPIs and target outcomes |
| [ ] 31 | Architecture and build-vs-buy | Architecture decision record |
| [ ] 32 | REST integrations and webhooks | ServiceNow-style ticket adapter |
| [ ] 33 | MCP and tool contracts | Read-only MCP server |
| [ ] 34 | Integration testing and customer demo | Ticket integration demonstration |
| [ ] 35 | Review, record and publish | YouTube #9 Customer discovery; #10 MCP integration; three Reels |

### Week 6: Agent orchestration and business workflows (Days 36–42)

**Weekly outcome:** Create controlled, stateful AI workflows.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 36 | Agents vs deterministic workflows | Orchestration decision document |
| [ ] 37 | LangGraph states, nodes and edges | Incident triage graph |
| [ ] 38 | Tool routing and multi-step workflows | Knowledge lookup and resolution suggestions |
| [ ] 39 | Human-in-the-loop approvals | Approval gate for ticket updates |
| [ ] 40 | Camunda 8 and long-running workflows | Mock or local approval integration |
| [ ] 41 | Checkpoints, retries and idempotency | Persistence and replay tests |
| [ ] 42 | Review, record and publish | YouTube #11 LangGraph; #12 LangGraph with Camunda; three Reels |

### Week 7: Production security and deployment (Days 43–49)

**Weekly outcome:** Securely deploy and operate the solution.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 43 | Threat modeling and tenant isolation | Security architecture and access matrix |
| [ ] 44 | Docker and container hardening | Containerized API, worker and database |
| [ ] 45 | GitHub Actions CI/CD | Lint, test and image-build pipeline |
| [ ] 46 | AWS deployment architecture | ECS, IAM, secrets and networking design |
| [ ] 47 | Staging deployment | Deployed app with health checks |
| [ ] 48 | Monitoring, runbooks and rollback | Operational dashboards and runbook |
| [ ] 49 | Review, record and publish | YouTube #13 Enterprise AI security; #14 AWS deployment; three Reels |

### Week 8: Performance, scalability, cost and ROI (Days 50–56)

**Weekly outcome:** Demonstrate reliability and business value.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 50 | Latency budgets and p95 | Latency instrumentation |
| [ ] 51 | Caching, concurrency and batching | Workflow performance improvements |
| [ ] 52 | Queues and resilience | Background processing design |
| [ ] 53 | LLM and infrastructure costs | Per-request cost calculator |
| [ ] 54 | Customer KPIs and ROI | Time-saved and payback model |
| [ ] 55 | Load tests and failure injection | Performance and reliability report |
| [ ] 56 | Review, record and publish | YouTube #15 AI performance; #16 Cost and ROI; three Reels |

### Week 9: Coding, SQL and backend interviews (Days 57–63)

**Weekly outcome:** Practice technical FDE interview skills.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 57 | Arrays, strings, maps and complexity | Three coding problems |
| [ ] 58 | Sliding window, two pointers and sorting | Three medium coding problems |
| [ ] 59 | Trees, graphs, BFS and DFS | Graph and dependency problems |
| [ ] 60 | SQL joins, aggregations and windows | Five practical SQL questions |
| [ ] 61 | Concurrency, async I/O and debugging | Fix a concurrent API issue |
| [ ] 62 | Live coding simulation | Timed API exercise |
| [ ] 63 | Review, record and publish | YouTube #17 Coding interview; #18 SQL/backend challenges; three Reels |

### Week 10: AI system design and architecture interviews (Days 64–70)

**Weekly outcome:** Defend architecture decisions under constraints.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 64 | AI system design methodology | Multi-tenant RAG design |
| [ ] 65 | Agent and workflow scaling | Thousands-of-tasks architecture |
| [ ] 66 | Data consistency and storage trade-offs | PostgreSQL, Redis and Kafka comparison |
| [ ] 67 | Security, availability and disaster recovery | Resilient architecture design |
| [ ] 68 | Model, evaluation and cost trade-offs | Architecture decision comparison |
| [ ] 69 | Timed system design mock | 45-minute architecture presentation |
| [ ] 70 | Review, record and publish | YouTube #19 Enterprise RAG design; #20 Scalable agents; three Reels |

### Week 11: Customer scenarios and FDE delivery (Days 71–77)

**Weekly outcome:** Practice customer-facing engineering.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 71 | Ambiguous customer requirements | Mock discovery meeting |
| [ ] 72 | Stakeholder alignment | Executive summary and technical plan |
| [ ] 73 | Scope changes and prioritization | Phased delivery proposal |
| [ ] 74 | Debugging and escalation | Incident simulation and RCA |
| [ ] 75 | Adoption and business impact | Outcome dashboard |
| [ ] 76 | Behavioral interviews and STAR | Six experience-based stories |
| [ ] 77 | Review, record and publish | YouTube #21 Customer scenario; #22 Delivery and escalation; three Reels |

### Week 12: Capstone, portfolio and interview readiness (Days 78–84)

**Weekly outcome:** Package the complete end-to-end FDE case study.

| Day | Learning focus | Deliverable / content |
|---|---|---|
| [ ] 78 | Capstone integration | API, RAG, agents, MCP and approvals |
| [ ] 79 | Final quality, security and load testing | Acceptance and readiness checklist |
| [ ] 80 | Deployment and demo preparation | Staging demo and architecture documentation |
| [ ] 81 | Customer presentation | Mock executive and engineering demo |
| [ ] 82 | Portfolio and case-study writing | README, diagrams, metrics and walkthrough |
| [ ] 83 | Final mock interview | Coding, design and behavioral simulation |
| [ ] 84 | Review and publish | YouTube #23 Capstone; #24 FDE interview roadmap; three Reels |
## Milestone acceptance criteria

| End of week | Milestone | Evidence |
|---|---|---|
| 2 | AI Incident Classifier MVP | API, LLM integration, tests and demo |
| 4 | Reliable RAG Service | pgvector, citations, evaluation and security tests |
| 6 | Enterprise Agent Workflow | MCP, LangGraph, approval controls and replay tests |
| 8 | Production-style deployment | Staging app, CI/CD, monitoring, latency and ROI report |
| 10 | System Design Portfolio | Diagrams, architecture decision records and mock interviews |
| 12 | Complete FDE Case Study | Working integrated demo, documentation and customer presentation |

## Definition of done for each engineering day

- [ ] Explain the concept and its relevance to a customer problem.
- [ ] Produce the promised code, design or written deliverable.
- [ ] Run appropriate tests and capture reproducible results.
- [ ] Document important trade-offs, limitations and follow-up work.
- [ ] Commit the changes with a meaningful message.
- [ ] Prepare tutorial notes or short-form content when relevant.

## Suggested repository layout

```text
FDE/
├── README.md
├── docs/
│   ├── architecture/
│   ├── customer-scenarios/
│   ├── decisions/
│   ├── evaluations/
│   └── tutorials/
├── backend/
├── agents/
├── integrations/
├── infrastructure/
├── tests/
└── content/
    ├── youtube/
    └── instagram/
```

This is a **target structure**, not a claim that all directories already exist. Add folders as their implementation begins.

## Git and collaboration workflow

- Keep `main` stable; use feature branches such as `week01/fastapi-foundations`.
- Commit tested, incremental changes and open pull requests for review.
- Never commit secrets, real customer data or generated credentials.
- The GitHub integration can create and edit repository files and manage supported remote operations; local Windows Git, Docker and VS Code commands must run on your computer.
- Document each week's implementation and link published Dpoint videos when available.

## Start here

**Day 1:** FDE vs SWE vs AI Engineer vs Solutions Architect; walk through the incident-management customer scenario and its lifecycle. Sketch the capstone architecture and prepare the first Dpoint tutorial.

**Day 2:** Set up Python 3.12+, uv, VS Code, Git, Docker, Ruff and pytest locally; create the initial FastAPI `/health` endpoint.

---

*Roadmap version: 1.0 — 24 October 2026. This README is the single source of truth for the synchronized FDE learning and Dpoint publishing plan.*

## Day 2 — Local Development Setup
- [Windows + macOS stack, installation and verified Windows versions](docs/day02-development-stack-and-installation.md)
- [Day 2 command reference (PowerShell and macOS Terminal)](docs/day02-command-reference.md)
- Windows local workspace: `E:\FDE`; project Python: `3.13` via `uv`.

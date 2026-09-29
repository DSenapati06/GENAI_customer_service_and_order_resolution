# 🤖 Production Agentic AI --- Interview Quick Reference

## 🎯 Use Case: Agentic AI Customer Service & Order Resolution Platform

### Purpose

An intelligent customer-support system that can **understand customer issues, retrieve company policies, check orders and shipments, process refunds, and coordinate multiple specialized AI agents** to resolve a request end-to-end.

### Example Customer Request

> **“My order 123 arrived damaged. Am I eligible for a refund? If yes, process it and tell me when I’ll get my money.”**

### Business Flow

```text
Customer Request
      ↓
Understand Intent
      ↓
Check Order
      ↓
Verify Delivery
      ↓
Retrieve Refund Policy
      ↓
Check Refund Eligibility
      ↓
Human Approval (if high value)
      ↓
Process Refund
      ↓
Notify Customer
```

### What This Use Case Demonstrates

- **RAG** → retrieve trusted refund/return policies.
- **Memory & State** → remember customer context and workflow progress.
- **Orchestration** → LangGraph controls the execution flow.
- **Skills** → reusable order, shipping, refund, and notification capabilities.
- **MCP** → standardized access to order, shipping, and payment systems.
- **Multi-Agent** → Supervisor, Order, Shipping, and Refund agents collaborate.
- **A2A** → independently deployed agents communicate with one another.
- **Harness** → prompts, context, tools, permissions, guardrails, and execution control around the LLM.
- **Evals** → validate retrieval, routing, tool calls, safety, and final responses.

It also demonstrates production patterns such as **Human-in-the-Loop, Kafka, retries/DLQ, security, tracing, observability, and cost monitoring**.

### One-Line Interview Description

> **“I designed an Agentic AI Customer Service & Order Resolution Platform that uses specialized agents, RAG, and enterprise tools to handle order, shipping, return, and refund requests end-to-end, with deterministic guardrails and human approval for sensitive actions.”**

---

> **Use case:** AI Customer Service & Order Resolution Platform\
> A production-ready reference covering **Multi-Agent, RAG, Memory, MCP,
> A2A, Skills, Evals, Harness, Human-in-the-Loop, Kafka, Guardrails, and
> Observability**.

------------------------------------------------------------------------

## 🎯 Business Use Case

A customer asks:

> **"Order 123 arrived damaged. Check whether I'm eligible for a refund,
> refund it if possible, and tell me when I'll receive the money."**

Instead of one huge agent, the platform uses specialized agents:

-   **Supervisor Agent** --- understands the request and routes work.
-   **Order Agent** --- validates customer/order information.
-   **Shipping Agent** --- verifies delivery status.
-   **Refund Agent** --- retrieves policy, checks eligibility, and
    initiates refund.
-   **Human Approval** --- required for high-value refunds.
-   **Kafka Worker** --- processes long-running refund operations
    asynchronously.

------------------------------------------------------------------------

## 🏗️ High-Level Architecture

``` mermaid
flowchart TD
    U[Customer] --> API[FastAPI + OAuth/JWT]
    API --> S[Supervisor Agent / LangGraph]

    S --> OA[Order Agent]
    S --> SA[Shipping Agent]
    S --> RA[Refund Agent]

    OA <-->|A2A| SA
    SA <-->|A2A| RA
    OA <-->|A2A| RA

    OA --> MCP[MCP Client]
    SA --> MCP
    RA --> MCP

    MCP --> OMS[Order MCP Server]
    MCP --> SMS[Shipping MCP Server]
    MCP --> PMS[Payment MCP Server]

    RA --> RAG[RAG Pipeline]
    RAG --> VDB[(PostgreSQL + pgvector)]

    RA --> HITL{Refund > $100?}
    HITL -->|Yes| H[Human Approval]
    HITL -->|No| K[Kafka]
    H --> K
    K --> W[Refund Worker]
    W --> PMS
```

**Around the whole system:** Guardrails • State/Memory • Tracing • Evals
• Retry/Timeout • Cost Monitoring

------------------------------------------------------------------------

# 🧩 9 Agentic AI Concepts

  -------------------------------------------------------------------------
  \#                Concept             Implementation    Easy Meaning
  ----------------- ------------------- ----------------- -----------------
  1                 **Memory & State**  Redis + LangGraph Remember history
                                        Checkpoint +      and workflow
                                        PostgreSQL        progress

  2                 **Orchestration**   LangGraph +       Decide what
                                        Supervisor Agent  happens next

  3                 **RAG**             LangChain +       Give agents
                                        Embeddings +      trusted
                                        pgvector          enterprise
                                                          knowledge

  4                 **Harness**         Prompt +          Runtime/control
                                        Context + Tools + layer around LLM
                                        Skills +          
                                        Guardrails        

  5                 **Evals**           LangSmith +       Measure
                                        pytest + Golden   correctness and
                                        Dataset           quality

  6                 **MCP**             MCP               Standardized
                                        Client/Servers    access to
                                                          tools/data

  7                 **Skills**          Reusable Python   Reusable agent
                                        capabilities      abilities

  8                 **A2A**             Agent-to-Agent    Independently
                                        communication     deployed agents
                                                          communicate

  9                 **Multi-Agent**     Supervisor +      Specialists
                                        specialist agents collaborate
  -------------------------------------------------------------------------

------------------------------------------------------------------------

# 🛠️ Tech Stack

  Layer                 Choice
  --------------------- ----------------------------------------
  Language              **Python**
  REST API              **FastAPI + Pydantic**
  Agent Orchestration   **LangGraph**
  LLM                   **OpenAI GPT model with tool calling**
  RAG Framework         **LangChain**
  Document Loader       **PyPDFLoader**
  Chunking              **RecursiveCharacterTextSplitter**
  Embeddings            **OpenAIEmbeddings**
  Vector DB             **PostgreSQL + pgvector**
  State / Cache         **Redis + LangGraph Checkpointer**
  Enterprise Tools      **MCP**
  Agent Communication   **A2A**
  Async Processing      **Kafka + Worker**
  HTTP Calls            **httpx**
  Retry                 **tenacity**
  Agent Tracing/Evals   **LangSmith**
  App Observability     **OpenTelemetry**
  Security              **OAuth2/JWT + RBAC**

------------------------------------------------------------------------

## 📦 requirements.txt

### API / Validation

```txt
fastapi
uvicorn[standard]
pydantic
python-dotenv
httpx
```

### LLM + Agent Framework

```txt
openai
langchain
langchain-core
langchain-openai
langchain-community
langgraph
```

### Multi-Agent / Supervisor

```txt
langgraph-supervisor
```

### RAG — Document Processing

```txt
pypdf
langchain-text-splitters
```

### Vector Database / PostgreSQL

```txt
langchain-postgres
psycopg[binary]
pgvector
```

### Memory / State

```txt
redis
langgraph-checkpoint-postgres
```

### MCP — Tool Integration

```txt
mcp
```

### Async Processing / Kafka

```txt
confluent-kafka
```

### Retry / Resilience

```txt
tenacity
```

### Observability / Tracing / Evals

```txt
langsmith
opentelemetry-api
opentelemetry-sdk
opentelemetry-exporter-otlp
```

### Testing

```txt
pytest
pytest-asyncio
```

---

## Complete `requirements.txt`

Copy this section into the actual `requirements.txt` file:

```txt
# API
fastapi
uvicorn[standard]
pydantic
python-dotenv
httpx

# LLM / Agent
openai
langchain
langchain-core
langchain-openai
langchain-community
langgraph
langgraph-supervisor

# RAG
pypdf
langchain-text-splitters

# Vector DB
langchain-postgres
psycopg[binary]
pgvector

# Memory / State
redis
langgraph-checkpoint-postgres

# MCP
mcp

# Kafka
confluent-kafka

# Retry
tenacity

# Observability / Evals
langsmith
opentelemetry-api
opentelemetry-sdk
opentelemetry-exporter-otlp

# Testing
pytest
pytest-asyncio
```

## Quick Interview Mapping

| Requirement | Purpose |
|---|---|
| `fastapi` | REST API |
| `pydantic` | Request/response validation |
| `langgraph` | Agent orchestration and workflow |
| `langgraph-supervisor` | Multi-agent supervisor |
| `langchain-openai` | LLM + embeddings |
| `langchain-text-splitters` | RAG chunking |
| `langchain-postgres` / `pgvector` | Vector storage and retrieval |
| `redis` | Fast state/cache |
| `langgraph-checkpoint-postgres` | Durable agent checkpoints |
| `mcp` | MCP integration |
| `confluent-kafka` | Async/event processing |
| `tenacity` | Retry/backoff |
| `langsmith` | Agent tracing/evaluation |
| `opentelemetry-*` | Application observability |
| `pytest` | Automated testing |

> **Interview memory:**  
> `FastAPI → LangGraph → LLM → RAG → pgvector → MCP → Multi-Agent → Kafka → Observability → Evals`

> **Production note:** Pin package versions after testing the compatible dependency set rather than leaving production dependencies unversioned.


# 📚 Important Python Libraries

``` python
# API / validation
from fastapi import FastAPI
from pydantic import BaseModel

# Agent orchestration
from langgraph.graph import StateGraph, START, END
from langgraph.types import interrupt
from langchain_core.tools import tool

# LLM / embeddings
from langchain_openai import ChatOpenAI, OpenAIEmbeddings

# RAG
from langchain_community.document_loaders import PyPDFLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_postgres import PGVector

# State / cache
import redis

# APIs
import httpx

# Async messaging
from confluent_kafka import Producer, Consumer

# Reliability
from tenacity import retry, stop_after_attempt, wait_exponential

# Observability
from opentelemetry import trace
```

------------------------------------------------------------------------

# 🔄 End-to-End Request Flow

``` text
Customer
   ↓
FastAPI
   ↓
Supervisor Agent
   ↓
LangGraph Router
   ↓
Order Agent ──MCP──> Order System
   ↓ A2A
Shipping Agent ──MCP──> Shipping System
   ↓ A2A
Refund Agent
   ↓
RAG → Retrieve Refund Policy
   ↓
Eligibility Check
   ↓
Deterministic Business Rules
   ↓
Refund > $100?
   ├── YES → Human Approval
   └── NO ──────────────┐
                        ↓
                      Kafka
                        ↓
                 Refund Worker
                        ↓
                 Payment MCP/API
                        ↓
                 Refund Complete
```

------------------------------------------------------------------------

# 1️⃣ RAG --- Ingestion Pipeline

## Input

``` text
refund-policy.pdf
```

## Pipeline

``` text
PDF
 ↓
Load
 ↓
Recursive Chunking
 ↓
Embeddings
 ↓
PostgreSQL + pgvector
```

### Why recursive chunking?

It attempts to preserve meaningful boundaries such as **paragraphs →
sentences → smaller text**, instead of blindly cutting documents.

Typical starting configuration:

``` text
Chunk size   ≈ 500–800 tokens
Overlap      ≈ 10–15%
```

### Pseudocode

``` python
docs = PyPDFLoader("refund-policy.pdf").load()

splitter = RecursiveCharacterTextSplitter(
    chunk_size=700,
    chunk_overlap=100
)

chunks = splitter.split_documents(docs)

embeddings = OpenAIEmbeddings(
    model="text-embedding-3-small"
)

vector_db = PGVector(
    embeddings=embeddings,
    connection=DB_URL,
    collection_name="refund_policies"
)

vector_db.add_documents(chunks)
```

------------------------------------------------------------------------

# 2️⃣ RAG --- Runtime Retrieval

Customer query:

``` text
"My order arrived damaged. Can I get a refund?"
```

Recommended production strategy:

``` text
Query
 ↓
Query Embedding
 ↓
Hybrid Retrieval
 ├── Semantic / Vector Search
 └── Keyword / BM25 Search
 ↓
Metadata Filtering
 ↓
Top-K Candidates
 ↓
Reranking
 ↓
Best 3–5 Chunks
 ↓
LLM + Context
 ↓
Grounded Answer
```

### Pseudocode

``` python
retriever = vector_db.as_retriever(
    search_kwargs={"k": 5}
)

policy_docs = retriever.invoke(
    "damaged product refund policy"
)
```

### Interview keywords

**Recursive Chunking → Embedding → pgvector → Cosine Similarity → Hybrid
Search → Metadata Filtering → Top-K → Reranking**

------------------------------------------------------------------------

# 3️⃣ Multi-Agent + Orchestration

``` text
                    Supervisor Agent
                          ↓
             ┌────────────┼────────────┐
             ↓            ↓            ↓
        Order Agent  Shipping Agent  Refund Agent
```

Use **LangGraph** to control the workflow.

``` python
class AgentState(TypedDict):
    message: str
    order_id: str
    order: dict
    policy: str
    amount: float
    approved: bool

graph = StateGraph(AgentState)

graph.add_node("router", route_request)
graph.add_node("order_agent", order_agent)
graph.add_node("shipping_agent", shipping_agent)
graph.add_node("refund_agent", refund_agent)

graph.add_edge(START, "router")
```

For routing, use **structured LLM output**:

``` python
class Route(BaseModel):
    agent: Literal["order", "shipping", "refund"]

router = llm.with_structured_output(Route)
```

### Why multiple agents?

Use them when domains have different:

-   Responsibilities
-   Prompts/context
-   Tools
-   Permissions
-   Deployment boundaries

> **Do not use multi-agent just because it sounds advanced.**

------------------------------------------------------------------------

# 4️⃣ Skills

A **skill** is a reusable business capability.

``` text
CheckOrderSkill
TrackShipmentSkill
CheckRefundEligibilitySkill
CreateRefundSkill
NotifyCustomerSkill
```

Example:

``` python
class RefundEligibilitySkill:

    def execute(self, order, policy):
        return validate_refund(order, policy)
```

Multiple agents can reuse the same skill.

------------------------------------------------------------------------

# 5️⃣ MCP --- Tools & Enterprise Data

Instead of hard-coding every integration into every agent:

``` text
Agent
 ↓
MCP Client
 ↓
MCP Servers
 ├── Order MCP Server
 ├── Shipping MCP Server
 └── Payment MCP Server
```

Example exposed tools:

``` text
Order MCP
  get_order()
  get_order_items()

Shipping MCP
  track_shipment()
  get_delivery_status()

Payment MCP
  create_refund()
  get_refund_status()
```

Conceptual pseudocode:

``` python
mcp_tools = await mcp_client.get_tools()

agent = llm.bind_tools(mcp_tools)
```

> **Interview:** MCP standardizes how agents discover and access
> external tools and context.

------------------------------------------------------------------------

# 6️⃣ A2A --- Agent-to-Agent Communication

Example:

``` text
Refund Agent
    ↓
"Verify delivery for order 123"
    ↓ A2A
Shipping Agent
    ↓
"Delivered; damage case exists"
    ↓ A2A
Refund Agent
```

Conceptual pseudocode:

``` python
result = await a2a_client.send_task(
    agent="shipping-agent",
    message={
        "order_id": "123",
        "task": "verify_delivery"
    }
)
```

### Remember

``` text
Multi-Agent = architecture of multiple collaborating agents
A2A         = communication between agents
MCP         = agents accessing tools/data
```

------------------------------------------------------------------------

# 7️⃣ Memory & State

## Workflow State

``` python
state = {
    "order_id": "123",
    "amount": 200,
    "policy_checked": True,
    "approval": "PENDING"
}
```

Use:

``` text
LangGraph Checkpoint + Redis
```

This supports:

``` text
Process
 ↓
Wait for approval
 ↓
PAUSE
 ↓
Manager approves later
 ↓
RESUME from checkpoint
```

## Long-Term Memory

Store only useful persistent information, for example:

``` text
Customer communication preference
Relevant prior resolved cases
Useful customer preferences
```

> **State = where the current workflow is.**\
> **Memory = useful information retained across interactions.**

------------------------------------------------------------------------

# 8️⃣ Harness

The LLM alone is **not** the agent.

``` text
┌──────────── AGENT HARNESS ─────────────┐
│ System Prompt                          │
│ Context                                │
│ Skills                                 │
│ Tools                                  │
│ Memory / State                         │
│ Permissions                            │
│ Guardrails                             │
│ Retry / Timeout                        │
│ Execution Control                      │
│                                        │
│                LLM                     │
└────────────────────────────────────────┘
```

Typical execution:

``` python
validate_input()
authorize_tool()
result = execute_tool()
validate_output(result)
```

> **Harness = runtime/control layer that turns the LLM into a controlled
> agent.**

------------------------------------------------------------------------

# 9️⃣ Guardrails + Human-in-the-Loop

Never let probabilistic LLM reasoning directly control critical
financial rules.

``` python
def validate_refund(state):

    if state["amount"] <= 0:
        return "reject"

    if not state["policy_eligible"]:
        return "reject"

    if state["amount"] > 100:
        return "human_approval"

    return "refund"
```

For a \$200 refund:

``` text
Refund Agent
 ↓
$200 > $100
 ↓
Human Approval
 ↓
Approved
 ↓
Continue Workflow
```

LangGraph:

``` python
approval = interrupt({
    "order_id": state["order_id"],
    "amount": state["amount"],
    "action": "REFUND"
})
```

### Key interview statement

> **LLM handles probabilistic reasoning; deterministic code handles
> critical business constraints.**

------------------------------------------------------------------------

# 🔟 Async Processing --- Kafka

Don't keep an HTTP request open for long-running processing.

``` python
from confluent_kafka import Producer

producer.produce(
    "refund-requests",
    value=refund_event
)
```

Worker:

``` text
Agent
 ↓
Kafka
 ↓
Refund Worker
 ↓
Payment MCP/API
```

Reliability:

``` text
Failure
 ↓
Retry + Exponential Backoff
 ↓
Still fails
 ↓
Dead Letter Queue (DLQ)
```

Python retry:

``` python
@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(min=1, max=10)
)
def call_payment_api():
    return payment_api.refund(...)
```

------------------------------------------------------------------------

# 1️⃣1️⃣ Evals

Build a **golden evaluation dataset**.

``` python
test_cases = [
    {
        "input": "Damaged item delivered 5 days ago",
        "expected": "REFUND_ALLOWED"
    },
    {
        "input": "Item purchased 90 days ago",
        "expected": "REFUND_DENIED"
    }
]
```

Evaluate:

  Eval             Question
  ---------------- ------------------------------------------------
  Retrieval        Did we retrieve the correct policy?
  Routing          Did supervisor select the correct agent?
  Tool Selection   Did the agent call the correct tool?
  Groundedness     Is the answer supported by retrieved evidence?
  Task Success     Was the workflow completed correctly?
  Safety           Were unauthorized actions blocked?
  Performance      Latency/token/cost acceptable?

Use:

``` text
LangSmith
pytest
Golden datasets
Deterministic assertions
LLM-as-a-Judge where subjective evaluation is required
```

Prefer deterministic checks when possible:

``` python
assert refund.amount == 200
assert refund.order_id == "123"
assert unauthorized_refund is False
```

------------------------------------------------------------------------

# 1️⃣2️⃣ Observability

Use **LangSmith + OpenTelemetry**.

``` text
Trace: request-123

Supervisor Agent        300 ms
Order Agent             400 ms
MCP get_order           120 ms
Shipping Agent          500 ms
RAG Retrieval           180 ms
Refund Agent            600 ms
Human Approval          ...
Kafka Worker            350 ms

Track:
✓ Latency
✓ Token usage
✓ Cost
✓ Tool calls
✓ Retrieval results
✓ Errors/retries
✓ Agent decisions
✓ Task success
```

------------------------------------------------------------------------

# 🔐 Security Checklist

``` text
✓ OAuth2 / JWT authentication
✓ RBAC authorization
✓ Tool-level permissions
✓ Input validation
✓ Output validation
✓ Prompt-injection protection
✓ PII protection
✓ Secret management
✓ Audit logs
✓ Rate limiting
✓ Human approval for risky actions
```

------------------------------------------------------------------------

# 🧠 Interview Cheat Sheet

``` text
INPUT
  ↓
FastAPI
  ↓
SUPERVISOR
  → LangGraph orchestration
  ↓
MULTI-AGENT
  → Order / Shipping / Refund
  ↓
A2A
  → Agent ↔ Agent
  ↓
MCP
  → Agent → Tools/Data
  ↓
RAG
  → Load
  → Recursive Chunk
  → Embed
  → pgvector
  → Hybrid Search
  → Rerank
  ↓
SKILLS
  → Reusable capabilities
  ↓
STATE
  → Redis + Checkpoint
  ↓
GUARDRAILS
  → Deterministic rules
  ↓
HITL
  → Human approval
  ↓
KAFKA
  → Worker + Retry + DLQ
  ↓
EVALS
  → Quality
  ↓
OBSERVABILITY
  → Trace + Metrics + Cost
```

------------------------------------------------------------------------

# ⚡ One-Line Definitions

  Term                Remember
  ------------------- ---------------------------------------------
  **RAG**             Give the agent trusted knowledge
  **Memory**          Remember information across interactions
  **State**           Track current workflow progress
  **Orchestration**   Control who does what and what happens next
  **Tool**            Let an agent perform an action
  **Skill**           Reusable agent capability
  **MCP**             Standardized connection to tools/context
  **A2A**             Agent-to-agent communication
  **Multi-Agent**     Specialized agents collaborating
  **Harness**         Runtime/control layer around the LLM
  **HITL**            Human approval/intervention
  **Eval**            Measure agent quality/correctness
  **Guardrail**       Prevent unsafe/invalid behavior

------------------------------------------------------------------------

# 🎤 60-Second Interview Answer

> "I designed an agentic customer-service platform using **FastAPI and
> LangGraph**. A supervisor routes requests to specialized **Order,
> Shipping, and Refund agents**. Each agent has reusable skills and
> accesses enterprise systems through **MCP servers**, while
> independently deployed agents can communicate through **A2A**.
>
> For enterprise knowledge, I use **RAG**: documents are recursively
> chunked, embedded, stored in **PostgreSQL with pgvector**, and
> retrieved using hybrid search with optional reranking. **Redis and
> LangGraph checkpoints** maintain workflow state.
>
> Critical financial rules are deterministic, and high-value refunds
> require **human approval**. Long-running operations use **Kafka
> workers with retries and DLQ**. The agent harness controls prompts,
> tools, permissions, state, and guardrails. Finally, I use **LangSmith
> and OpenTelemetry** for evaluation, tracing, latency, token usage, and
> cost monitoring."

------------------------------------------------------------------------

# ⭐ Architecture Decisions Interviewers May Ask

**Why LangGraph?**\
Stateful workflows, conditional routing, checkpoints, human-in-the-loop,
and multi-step agent orchestration.

**Why pgvector?**\
Keeps relational data and vector search in PostgreSQL; convenient when
the organization already uses Postgres.

**Why hybrid retrieval?**\
Semantic search understands meaning; keyword/BM25 is strong for exact
IDs, product names, and terminology. Combining them improves retrieval
coverage.

**Why reranking?**\
Initial retrieval finds candidates quickly; reranking improves the final
ordering before context is sent to the LLM.

**Why Kafka?**\
Decouples long-running processing from synchronous requests and supports
scalable asynchronous workers.

**Why deterministic guardrails?**\
Critical rules such as refund limits should not depend solely on
probabilistic model output.

**MCP vs A2A?**

``` text
MCP → Agent ↔ Tools / Data
A2A → Agent ↔ Agent
```

**Why Multi-Agent?**\
Use it when responsibilities, permissions, prompts, tools, or deployment
boundaries are genuinely different---not simply to make the architecture
more complex.

------------------------------------------------------------------------

## 🔑 Final Memory Trick

``` text
FastAPI
   ↓
LangGraph
   ↓
Supervisor
   ↓
Multi-Agent + A2A
   ↓
MCP Tools
   ↓
RAG
   ↓
Memory / State
   ↓
Guardrails + HITL
   ↓
Kafka
   ↓
Evals + Observability
```

### **Reason → Retrieve → Act → Control → Observe**

------------------------------------------------------------------------

> ⭐ **Interview principle:** Don't just name technologies. Explain
> **why each component exists, what problem it solves, and when you
> would not use it.**

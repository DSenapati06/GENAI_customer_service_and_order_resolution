# Agentic AI Customer Service Platform — Simple, Interview-Friendly Explanation

Think about the whole architecture as **one customer request traveling through the system**.

Example:

> **“My order 123 arrived damaged. Please refund my money.”**

## Simple step-by-step flow

### 1. Customer → FastAPI

The customer sends the request from a web app, mobile app, or chat.

```text
Customer
   ↓
FastAPI
```

FastAPI handles **authentication, validation, rate limiting, and logging**.

**Remember:** `FastAPI = Entry Gate`

---

### 2. Supervisor Agent understands the request

The LangGraph Supervisor receives:

```text
"Order 123 is damaged. Refund it."
```

It coordinates the workflow and decides which agent should work.

```text
FastAPI
   ↓
Supervisor Agent
   ↓
Order / Shipping / Refund
```

**Remember:** `Supervisor = Manager`

---

### 3. Jev makes fast, bounded decisions; the LLM handles deeper reasoning

Conceptually:

```text
             Supervisor
                 ↓
        ┌────────┴────────┐
       Jev               LLM
        ↓                 ↓
 Fast decision       Deep reasoning
 Routing             Planning
 Classification      Explanation
 Scoring             Complex decisions
```

Easy memory:

> **Jev = Decide fast**  
> **LLM = Think**

---

### 4. Supervisor delegates to specialized agents

For this request:

```text
Supervisor
    ↓
Order Agent
    ↓
Shipping Agent
    ↓
Refund Agent
```

Each agent has one job:

- **Order Agent** → verify order 123.
- **Shipping Agent** → verify delivery and damage status.
- **Refund Agent** → determine refund eligibility and initiate the refund.

**Remember:** `One Agent = One Responsibility`

---

### 5. Agents communicate using A2A

Example:

```text
Refund Agent
     ↓ A2A
Shipping Agent

"Was order 123 delivered or damaged?"

     ↓

"Yes, it was delivered and a damage case exists."
```

**Remember:** `A2A = Agent talks to Agent`

---

### 6. Refund Agent uses RAG

The agent should not invent company refund policies.

```text
Policy PDFs
    ↓
Chunk
    ↓
Embedding
    ↓
pgvector
    ↓
Hybrid Search
    ↓
Relevant Policy
    ↓
Refund Agent
```

For example, retrieval finds:

> “Damaged products can be refunded within 30 days.”

Important technologies:

```text
Loader     → PyPDFLoader
Chunking   → RecursiveCharacterTextSplitter
Embedding  → OpenAIEmbeddings
Vector DB  → PostgreSQL + pgvector
Search     → Hybrid Search + Reranking
```

**Remember:** `RAG = Give Agent Knowledge`

---

### 7. MCP connects agents to real systems

The agent needs real order, payment, and shipping data.

Instead of giving the LLM direct database access:

```text
Agent
 ↓
MCP Client
 ↓
MCP Server
 ↓
Enterprise System
```

For example:

```text
Order Agent
   ↓
Order MCP
   ↓
Order Management System
```

Or:

```text
Refund Agent
   ↓
Payment MCP
   ↓
Payment Gateway
```

**Remember:** `MCP = Agent connects to Tools/Data`

---

### 8. Skills are reusable capabilities

Examples:

```text
CheckOrderSkill
RefundEligibilitySkill
CreateRefundSkill
TrackShipmentSkill
NotifyCustomerSkill
```

The Refund Agent can combine several skills:

```text
Refund Agent
    ↓
CheckOrder
    ↓
CheckEligibility
    ↓
CreateRefund
```

**Remember:** `Skill = Reusable Ability`

---

### 9. Memory and State remember progress

Suppose manager approval is required. The system remembers:

```text
Order ID       = 123
Policy checked = Yes
Eligible       = Yes
Amount         = $200
Approval       = Pending
```

Technologies:

```text
Redis      → fast/session state
PostgreSQL → persistent information
LangGraph  → workflow checkpoints
```

**Remember:**

> `State = Where am I now?`  
> `Memory = What do I know or remember?`

---

### 10. Human approval protects important actions

For example:

```text
Refund <= $100
     ↓
Automatic

Refund > $100
     ↓
Human Approval
```

Our `$200` refund:

```text
Refund Agent
     ↓
$200
     ↓
Manager Approval
     ↓
Approved
```

**Remember:** `HITL = Human controls risky actions`

---

### 11. Kafka handles long-running work

After approval:

```text
Refund Agent
     ↓
Kafka
     ↓
Refund Worker
     ↓
Payment System
```

If payment fails:

```text
Fail
 ↓
Retry
 ↓
Retry
 ↓
DLQ
```

**Remember:** `Kafka + Worker = Do long work asynchronously`

---

### 12. Harness controls the agent

Think of the harness as a **safety and control box around the LLM and agents**.

```text
┌────── Agent Harness ──────┐
│ Prompt                    │
│ Context                   │
│ Tools                     │
│ Skills                    │
│ Permissions               │
│ Guardrails                │
│ Retry / Timeout           │
│ Human Approval            │
│                           │
│       LLM / Agent         │
└───────────────────────────┘
```

**Remember:** `Harness = Control the Agent`

---

### 13. Evals check whether the agent is good

Test questions:

```text
Did RAG retrieve the correct policy?     ✓
Did the supervisor select Refund Agent?  ✓
Did the agent call the correct tool?      ✓
Was the refund amount correct?            ✓
Was an unauthorized refund blocked?       ✓
```

Use:

```text
LangSmith
pytest
Golden Dataset
LLM-as-Judge (when appropriate)
```

**Remember:** `Evals = Is my Agent correct?`

---

### 14. Observability tells us what happened

Example:

```text
Request
 ↓
Supervisor       200ms
 ↓
Order Agent      300ms
 ↓
RAG              150ms
 ↓
Refund Agent     400ms
 ↓
Payment          250ms
```

Monitor:

**latency + errors + tool calls + tokens + cost + traces**

Use **LangSmith + OpenTelemetry**.

**Remember:** `Observability = What happened inside?`

---

## The whole architecture in one flow

This is the part to memorize for interviews:

```text
CUSTOMER
   ↓
FastAPI
   ↓
SUPERVISOR (LangGraph)
   ↓
Jev + LLM
   ↓
SPECIALIZED AGENTS
Order ↔ Shipping ↔ Refund
        A2A
   ↓
RAG → Knowledge
   ↓
MCP → Tools / Enterprise Systems
   ↓
Human Approval
   ↓
Kafka → Worker
   ↓
Payment / Order / Shipping Systems
   ↓
CUSTOMER RESPONSE
```

And **around everything**:

```text
Memory / State
Skills
Harness / Guardrails
Evals
Observability
```

### Easiest memory trick

Think:

**Enter → Manage → Think → Delegate → Know → Connect → Act → Monitor**

```text
ENTER       → FastAPI
MANAGE      → LangGraph Supervisor
THINK       → Jev + LLM
DELEGATE    → Multi-Agent + A2A
KNOW        → RAG + Memory
CONNECT     → MCP + Skills
ACT         → HITL + Kafka + Workers
MONITOR     → Evals + Observability
```

If you can remember these **eight words**, you can reconstruct almost the entire architecture during an interview.

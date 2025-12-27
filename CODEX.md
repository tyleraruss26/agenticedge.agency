# AgenticEdge — Codex Repository Instructions

## 0. Purpose of This Repository

This repository implements AgenticEdge, a production-grade agentic AI platform that deploys autonomous, policy-governed agents using Model Context Protocol (MCP) connectors.

The system enables AI agents to:
- Read real business context (calendar, email, docs, CRM)
- Reason with policies and thresholds
- Take real actions safely
- Log, audit, and learn continuously

This is not a chatbot repo. This is an agent runtime and orchestration system.

---

## 1. Non-Negotiable Design Principles

Codex MUST follow these principles at all times:

### 1.1 Agentic, Not Scripted
- Prefer agents with reasoning loops over static workflows
- No hard-coded business logic without policy abstraction
- Decisions must be explainable and logged

### 1.2 MCP First
- All integrations MUST use MCP connectors
- Do NOT implement direct REST API calls unless explicitly instructed
- Treat MCP as the canonical integration layer

### 1.3 Policy-Driven Execution
- All actions must pass through a policy evaluation layer
- No agent may execute irreversible actions without:
  - Confidence score
  - Approval rule
  - Audit log

### 1.4 Human-in-the-Loop by Default
- Escalation paths must exist
- Confidence thresholds gate autonomy
- Manual override is mandatory for high-risk actions

---

## 2. Repository Structure (Authoritative)

Codex MUST respect and extend this structure:

```
/agenticedge
│
├── agents/
│   ├── base_agent.ts
│   ├── chief_of_staff/
│   │   ├── agent.ts
│   │   ├── policies.yaml
│   │   └── prompts.md
│   ├── sales_agent/
│   ├── ops_agent/
│   ├── compliance_agent/
│   └── support_agent/
│
├── orchestration/
│   ├── router.ts
│   ├── confidence.ts
│   └── escalation.ts
│
├── policies/
│   ├── engine.ts
│   ├── schemas.ts
│   └── default_policies.yaml
│
├── mcp/
│   ├── connectors.ts
│   ├── permissions.ts
│   └── registry.ts
│
├── memory/
│   ├── logs.ts
│   ├── metrics.ts
│   └── vector_store.ts
│
├── dashboard/
│   ├── api.ts
│   └── schemas.ts
│
├── types/
│   └── agent.ts
│
└── README.md
```

Codex may add files, but must not flatten or bypass this hierarchy.

---

## 3. Agent Contract (Mandatory)

Every agent must extend BaseAgent and implement the following interface:

```ts
interface AgentExecution {
  context: AgentContext;
  decision: AgentDecision;
  actions: AgentAction[];
  confidence: number;
  auditLog: AuditEntry;
}
```

Codex must ensure:
- confidence ∈ [0,1]
- auditLog is always written
- actions are policy-approved

---

## 4. Base Agent Behavior (Required)

When generating BaseAgent, Codex must enforce:

1. **Context Gathering**
   - Use MCP connectors only
   - No mock data unless explicitly flagged
2. **Reasoning Step**
   - Explicit reasoning function
   - Short, structured trace (not chain-of-thought leakage)
3. **Policy Evaluation**
   - Evaluate YAML-defined policies
   - Return `ALLOW | DENY | ESCALATE`
4. **Action Execution**
   - Only after policy approval
   - Retry-safe, idempotent where possible
5. **Logging**
   - Write logs to `memory/logs.ts`
   - Emit metrics to `memory/metrics.ts`

---

## 5. Policy Engine Rules

Codex must treat policies as first-class system logic.

**Policy Format**

```yaml
- name: focus_protection
  if: meeting.conflicts_focus == true
  then: PROPOSE_ALTERNATIVE
```

**Policy Engine Requirements**
- Deterministic evaluation
- Schema validation
- Versionable policies
- Human-readable explanations

No business logic may bypass the policy engine.

---

## 6. MCP Connector Rules

When writing MCP-related code:
- Assume connectors expose:
  - `read()`
  - `write()`
  - `search()`
- Permissions must be declared in `mcp/permissions.ts`
- All connector calls must be traceable

Codex must never:
- Hardcode OAuth flows
- Store credentials in code
- Assume connector availability without checks

---

## 7. Confidence & Escalation Logic

Codex must always include:

```ts
if (confidence < CONFIDENCE_THRESHOLD) {
  escalateToHuman(...)
}
```

Escalation must:
- Preserve full context
- Include reasoning summary
- Be resumable

---

## 8. Logging & Metrics (Mandatory)

Every agent action must emit:

**Log Entry**

```json
{
  "agent": "chief_of_staff",
  "action": "schedule_meeting",
  "inputs": {},
  "outputs": {},
  "confidence": 0.92,
  "policy_hits": ["agenda_required"],
  "timestamp": "ISO-8601"
}
```

**Metrics**
- Time saved (estimated)
- Actions automated
- Human interventions
- Policy denials

---

## 9. Testing Expectations

Codex must generate:
- Unit tests for policy logic
- Mock MCP connectors for tests
- Scenario tests for agent flows

No PR is complete without tests.

---

## 10. Documentation Rules

Codex must:
- Update `README.md` when adding major features
- Add `prompts.md` for each agent
- Document policies in plain English

---

## 11. What Codex Should NEVER Do

- ❌ Generate UI without schema alignment
- ❌ Skip policy checks
- ❌ Embed secrets
- ❌ Hardcode business rules
- ❌ Create “magic” automation without logs

---

## 12. Canonical Agent Example

When in doubt, Codex should model behavior after:

**AgenticEdge Chief of Staff**
- Calendar-aware
- Policy-governed
- Audit-logged
- Confidence-scored
- Human-overridable

---

## 13. Final Instruction to Codex

You are building AI staff, not scripts. Every decision must be contextual, governed, auditable, and reversible.

---

If you want, next I can:
- Generate the initial repo scaffold
- Write the BaseAgent + Policy Engine
- Produce Chief of Staff v1 code
- Create GitHub issue templates & PR rules

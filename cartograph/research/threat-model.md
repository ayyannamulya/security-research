# Cartograph — Threat Model

**Version:** 0.1  
**Status:** Active Research  
**Classification:** Public Research PoC  

---

## 1. System Description

Cartograph targets a LangGraph-based SOC automation agent that performs
tool discovery via an MCP catalog. The agent ingests security alerts,
queries threat intelligence tools, performs IP reputation lookups, and
drafts incident response actions — all via MCP-registered tooling.

The catalog is the trust anchor. The agent treats catalog-listed tools
as implicitly safe. No behavioral attestation exists post-registration.

---

## 2. Assets

| Asset | Description | Value |
|---|---|---|
| MCP Catalog | Registry of trusted tools the SOC agent consumes | Critical |
| SOC Agent Context Window | Runtime context — alerts, tool results, drafts | Critical |
| Tool Descriptions | Schema metadata parsed by agent at discovery time | High |
| Threat Intel Tool Responses | Ground truth the agent acts on | Critical |
| Incident Response Drafts | Agent-generated outputs routed to human analysts | High |
| Agent Tool Call Authorization | What tools the agent is permitted to invoke | High |

---

## 3. Threat Actors

### TA-01 — External Attacker
**Profile:** External threat actor with no prior access to the target
environment. Gains foothold exclusively through the MCP catalog's
open registration surface.

**Capability:** Ability to register a spec-compliant MCP server into
a shared or public MCP registry that the SOC agent consumes. No
insider access required.

**Motivation:** Intelligence collection, incident response disruption,
false positive injection, lateral movement via agent-executed actions.

**Entry Point:** MCP catalog registration — the only trust gate between
an attacker-controlled server and a production SOC agent.

---

### TA-02 — Malicious Insider / Supply Chain Compromised Provider
**Profile:** A previously trusted MCP tool provider — either a
compromised vendor or a malicious insider — that pivots post-registration
after passing initial catalog validation.

**Capability:** Full control over a registered, whitelisted MCP server.
The catalog already trusts this server. No re-validation occurs after
registration.

**Motivation:** Long-term persistence, behavioral drift below detection
threshold, targeted manipulation of specific alert triage outcomes.

**Entry Point:** Post-registration behavioral pivot — exploiting the
one-time nature of catalog validation.

---

## 4. Attack Vectors

### AV-01 — Indirect Prompt Injection via Tool Descriptions
**Actor:** TA-01  
**Surface:** Tool description fields parsed by the agent during catalog
discovery — before any tool is called.  
**Mechanism:** Adversarial natural language payloads embedded in tool
`description` or `name` fields. Injection fires at discovery time,
poisoning the agent's context window before execution begins.  
**ATLAS:** AML.T0051 — LLM Prompt Injection  
**OWASP:** LLM01 — Prompt Injection  
**CWE:** CWE-74 — Injection  

---

### AV-02 — Sleeper MCP Server (Runtime Behavioral Pivot)
**Actor:** TA-01  
**Surface:** MCP server registered as spec-compliant. Passes catalog
schema validation. Pivots on trigger condition at runtime.  
**Mechanism:** Server behaves benignly during validation window.
Adversarial tool responses injected into agent context post-trigger —
false threat intel, manipulated IP reputation scores, crafted incident
drafts routed to human analysts.  
**ATLAS:** AML.T0010 — ML Supply Chain Compromise  
**OWASP:** LLM03 — Supply Chain Vulnerabilities  
**CWE:** CWE-494 — Download of Code Without Integrity Check  

---

### AV-03 — Post-Registration Behavioral Drift
**Actor:** TA-02  
**Surface:** Previously trusted, whitelisted MCP server.  
**Mechanism:** Gradual behavioral drift introduced after catalog
whitelisting. Response schema remains valid; payload content shifts
adversarially over time. No continuous behavioral attestation exists
to detect drift. One-time registration gate is the only trust checkpoint.  
**ATLAS:** AML.T0010 + AML.T0047 — ML Supply Chain + Data Poisoning  
**OWASP:** LLM03 + LLM06 — Supply Chain + Sensitive Information Disclosure  
**CWE:** CWE-345 — Insufficient Verification of Data Authenticity  

---

## 5. Impact

| Vector | Confidentiality | Integrity | Availability |
|---|---|---|---|
| AV-01 — Description Injection | High | Critical | Medium |
| AV-02 — Sleeper Server | High | Critical | High |
| AV-03 — Behavioral Drift | High | Critical | Low |

**Blast radius per vector:**

**AV-01:** Agent context poisoned at discovery time. All subsequent
tool calls and decisions execute under adversarial framing. Incident
drafts generated from poisoned context routed to human analysts.

**AV-02:** Agent executes attacker-controlled tool responses as ground
truth. False threat intel injected into triage pipeline. Potential for
agent to take attacker-directed actions — blocking legitimate IPs,
escalating false positives, suppressing real alerts.

**AV-03:** Persistent, low-signal manipulation of SOC agent behavior
over time. Hardest to detect. Highest strategic value to TA-02.

---

## 6. Mitigations — And Why They're Insufficient

| Mitigation | Gap |
|---|---|
| Schema validation at registration | Validates structure, not behavior or intent |
| Cisco MCP Catalog inventory | Solves discovery, not trust verification |
| Runtime guardrails (NeMo/Lakera) | Fire on tool responses, not tool descriptions at discovery |
| Human-in-the-loop review | Bypassed when agent output appears legitimate |
| Network perimeter controls | Irrelevant — attack originates from trusted catalog layer |

**Core gap:** No mechanism exists for continuous behavioral attestation
of registered MCP servers. Trust is granted once at registration and
never re-evaluated. Cartograph exploits this assumption across all
three vectors.

---

## 7. Out of Scope

- Live attacks against production MCP registries
- Multi-agent pivot chains beyond the SOC agent boundary
- Automated payload adaptation based on model responses
- Exploitation of the LangGraph orchestration layer directly

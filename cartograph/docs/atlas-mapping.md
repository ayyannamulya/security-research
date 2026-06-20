# Cartograph — ATLAS Mapping

**Version:** 0.1  
**Status:** Active Research  
**References:** `research/threat-model.md` · `research/attack-chains.md`

---

## Taxonomy Coverage

| Framework | Coverage |
|---|---|
| MITRE ATLAS | Primary — technique-level mapping per vector |
| OWASP LLM Top 10 | Secondary — category mapping per vector |
| NIST AI RMF | Governance mapping — function + category |
| CWE | Weakness-level mapping per vector |

---

## AV-01 — Indirect Prompt Injection via Tool Descriptions

### MITRE ATLAS

| Tactic | Technique | ID | Rationale |
|---|---|---|---|
| Initial Access | LLM Prompt Injection | AML.T0051 | Adversarial payload delivered via tool description field into agent context window at discovery time |
| Execution | LLM Plugin Compromise | AML.T0054 | MCP tool registration exploited as the delivery mechanism for context poisoning |
| Impact | Evade ML Model | AML.T0015 | Agent reasoning manipulated — downstream decisions execute under adversarial framing |

**Primary:** AML.T0051 — LLM Prompt Injection  
**Sub-technique:** Indirect — payload delivered through environment input (catalog metadata), not direct user interaction

### OWASP LLM Top 10

| Category | ID | Rationale |
|---|---|---|
| Prompt Injection | LLM01:2025 | Indirect injection via tool description field — attacker influences agent behavior through trusted environmental input |
| Supply Chain Vulnerabilities | LLM03:2025 | MCP catalog is a supply chain input; poisoned tool metadata is an upstream trust failure |

### NIST AI RMF

| Function | Category | Subcategory |
|---|---|---|
| GOVERN | Policy & Procedures | AI system inputs not validated for adversarial content prior to context loading |
| MAP | AI Risk Identification | Indirect injection via catalog metadata not modeled as an attack vector |
| MEASURE | AI Risk Measurement | No semantic inspection of tool descriptions at registration or discovery time |
| MANAGE | AI Risk Response | No guardrail coverage for pre-execution context poisoning window |

### CWE

| ID | Name | Rationale |
|---|---|---|
| CWE-74 | Injection | Adversarial content injected into agent context via unsanitized tool description field |
| CWE-20 | Improper Input Validation | Tool description content not validated for adversarial semantic payload |
| CWE-116 | Improper Encoding or Escaping of Output | Catalog metadata rendered into agent context without sanitization |

---

## AV-02 — Sleeper MCP Server (Runtime Behavioral Pivot)

### MITRE ATLAS

| Tactic | Technique | ID | Rationale |
|---|---|---|---|
| Initial Access | LLM Plugin Compromise | AML.T0054 | Malicious MCP server registered through legitimate catalog channel |
| Persistence | ML Supply Chain Compromise | AML.T0010 | Server persists in trusted catalog post-registration — no re-validation triggered |
| Defense Evasion | Evade ML Model | AML.T0015 | Benign behavior during validation window evades detection; pivot occurs post-whitelisting |
| Impact | Craft Adversarial Data | AML.T0043 | Adversarial tool responses crafted to manipulate SOC agent triage decisions |

**Primary:** AML.T0010 — ML Supply Chain Compromise  
**Secondary:** AML.T0054 — LLM Plugin Compromise

### OWASP LLM Top 10

| Category | ID | Rationale |
|---|---|---|
| Supply Chain Vulnerabilities | LLM03:2025 | MCP server is a supply chain component — compromise post-registration is a supply chain attack |
| Excessive Agency | LLM06:2025 | SOC agent acts on adversarial tool responses without independent verification — blast radius amplified by agent autonomy |
| Sensitive Information Disclosure | LLM02:2025 | Alert context and threat intel data exposed to attacker-controlled server via tool call payloads |

### NIST AI RMF

| Function | Category | Subcategory |
|---|---|---|
| GOVERN | Accountability | No continuous behavioral attestation of registered MCP servers post-whitelisting |
| MAP | Third-Party Risk | MCP servers treated as trusted third-party components without ongoing verification |
| MEASURE | Monitoring & Feedback | No runtime monitoring for behavioral drift in registered tool servers |
| MANAGE | Incident Response | No detection primitive exists to flag sleeper pivot events |

### CWE

| ID | Name | Rationale |
|---|---|---|
| CWE-494 | Download of Code Without Integrity Check | Tool server behavior consumed without integrity verification at runtime |
| CWE-345 | Insufficient Verification of Data Authenticity | Tool responses accepted as ground truth without authenticity verification |
| CWE-1357 | Reliance on Insufficiently Trustworthy Component | Agent relies on catalog trust signal that is not continuously maintained |
| CWE-441 | Unintended Proxy or Intermediary | Whitelisted MCP server acts as attacker-controlled intermediary between agent and threat intel |

---

## AV-03 — Post-Registration Behavioral Drift

### MITRE ATLAS

| Tactic | Technique | ID | Rationale |
|---|---|---|---|
| Initial Access | ML Supply Chain Compromise | AML.T0010 | Trusted tool provider compromised — supply chain entry point |
| Persistence | Poison Training Data | AML.T0007 | Cumulative drift in tool responses corrupts agent's operational ground truth over time — functionally equivalent to data poisoning at inference time |
| Defense Evasion | Evade ML Model | AML.T0015 | Drift magnitude per response kept below detection threshold by design |
| Impact | Craft Adversarial Data | AML.T0043 | Tool responses crafted to produce targeted triage failures — false negatives on specific threat actor TTPs |

**Primary:** AML.T0010 — ML Supply Chain Compromise  
**Secondary:** AML.T0007 — Poison Training Data (inference-time analog)

### OWASP LLM Top 10

| Category | ID | Rationale |
|---|---|---|
| Supply Chain Vulnerabilities | LLM03:2025 | Compromised trusted vendor is a direct supply chain attack |
| Sensitive Information Disclosure | LLM02:2025 | Alert context exposed to drifting server over extended production period |
| Excessive Agency | LLM06:2025 | Agent autonomy amplifies drift impact — no human checkpoint between tool response and triage decision |
| Misinformation | LLM09:2025 | Systematic corruption of threat intel ground truth produces confident, authoritative misinformation routed to analysts |

### NIST AI RMF

| Function | Category | Subcategory |
|---|---|---|
| GOVERN | Third-Party Risk Management | No contractual or technical mechanism for continuous behavioral attestation of vendor-supplied MCP servers |
| MAP | AI Risk Identification | Gradual behavioral drift not modeled as an attack vector in current MCP threat models |
| MEASURE | AI Performance Monitoring | No behavioral baseline established at registration — no reference point for drift detection |
| MANAGE | AI Risk Response | No remediation playbook exists for post-registration behavioral drift in agentic tool pipelines |

### CWE

| ID | Name | Rationale |
|---|---|---|
| CWE-345 | Insufficient Verification of Data Authenticity | Drifting responses accepted as authentic ground truth without verification against baseline |
| CWE-1357 | Reliance on Insufficiently Trustworthy Component | Long-term trust history actively degrades detection sensitivity |
| CWE-693 | Protection Mechanism Failure | Absence of continuous behavioral attestation allows drift to operate undetected indefinitely |
| CWE-235 | Improper Handling of Extra Parameters | Supplementary metadata fields exploited for context poisoning while primary fields remain valid |

---

## Cross-Vector Taxonomy Summary

| Vector | Primary ATLAS | Primary OWASP | Primary CWE | NIST Gap |
|---|---|---|---|---|
| AV-01 — Description Injection | AML.T0051 | LLM01:2025 | CWE-74 | MEASURE — no semantic inspection at discovery |
| AV-02 — Sleeper Server | AML.T0010 | LLM03:2025 | CWE-494 | MANAGE — no behavioral attestation post-registration |
| AV-03 — Behavioral Drift | AML.T0010 | LLM03:2025 | CWE-345 | MEASURE — no behavioral baseline at T0 |

---

## Shared Root Weakness

All three vectors trace to a single missing control:

> **CWE-693 — Protection Mechanism Failure**  
> The MCP catalog trust model relies on a one-time structural validation
> gate with no continuous behavioral verification. This architectural
> decision is the root weakness enabling all three documented vectors.

No ATLAS technique, OWASP category, or CWE entry directly describes
catalog-layer trust abuse in agentic pipelines as a first-class attack
surface. Cartograph documents this gap.

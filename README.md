# security-research

> Attacker-first. ATLAS-mapped. Reproducible.

Independent vulnerability research targeting production agentic AI systems —
where the attack surface is the architecture itself.

---

## What This Is

This repository is a collection of original security research across agentic
AI attack surfaces. Each piece is independently scoped, adversarially framed,
and published as a reproducible research artifact — not a demo, not a tutorial.

Research here starts from real threat models and ends at exploitable attack
chains. The goal is to surface vulnerability classes that exist in production
agentic systems before defenders know to look for them.

---

## Research Domains

```javascript
const research = {
  focus:    "Production agentic systems — tool-use pipelines, multi-agent orchestration, " +
            "and LLM-powered automation where trust boundaries collapse under adversarial pressure.",
  contains: "A growing collection of independent security research — each piece " +
            "independently scoped, adversarially framed, and mapped to industry taxonomies.",
  domains:  ["AI Red Teaming", "Agentic Pipelines", "MCP Security",
             "ML Supply Chain", "Prompt Injection", "Multi-Agent Systems",
             "Trust Boundaries", "LLM Evaluation"],
};
```

---

## Taxonomy

All research is mapped to one or more of the following:

- **MITRE ATLAS** — Adversarial threat landscape for AI systems
- **OWASP LLM Top 10** — LLM-specific vulnerability classification
- **NIST AI RMF** — AI risk management framework
- **CWE** — Common weakness enumeration

---

## Research Index

| # | Research | Attack Surface | Status |
|---|---|---|---|
| 01 | [Cartograph](./cartograph/) | MCP Catalog Poisoning · LangGraph SOC Agents | `Active` |

---

## Structure

Each research piece follows a consistent structure:

```
[research-name]/
├── README.md              # Overview, threat model summary, key findings
├── research/
│   ├── threat-model.md    # Assets, actors, vectors, impact, mitigations
│   └── attack-chains.md   # Attack chain documentation per vector
└── docs/
    ├── atlas-mapping.md   # Full MITRE ATLAS + OWASP + CWE taxonomy mapping
    └── findings.md        # Executive summary → technical depth → severity ratings
```

---

## Methodology

Every piece in this repository follows the same research methodology:

**1. Threat Modeling** — Define the system, assets, actors, and attack surface
before any attack chain is documented. STRIDE-informed, ATLAS-mapped.

**2. Attack Chain Documentation** — Each vector is documented at the mechanism
level — not just what the attack does, but why the trust model allows it and
what the blast radius looks like in a production environment.

**3. Detection Gap Analysis** — Every piece includes an explicit assessment of
why existing tooling fails to detect or prevent the documented vectors. If a
vendor claims coverage, that claim is tested against the attack chain.

**4. Taxonomy Mapping** — Every vector is mapped to MITRE ATLAS, OWASP LLM
Top 10, NIST AI RMF, and CWE. No finding is published without a taxonomy anchor.

---

## Responsible Disclosure

All research in this repository targets conceptual attack surfaces and
controlled environments. No research involves live attacks against production
systems, real MCP registries, or third-party infrastructure.

If any finding in this repository is relevant to a specific vendor or product,
responsible disclosure will be conducted prior to publication.

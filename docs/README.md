# Docs

This folder contains human-readable documentation for each ontology module in `ontologies/*.ttl`.

## Modules

- `ontologies/ontology.ttl` → `docs/ontology.md` (umbrella imports)
- **Overview**: `docs/upper-ontology-extensions.md` (what this ontology adds beyond PROV-O / P-PLAN / EP-PLAN)
- `ontologies/core.ttl` → `docs/core.md`
- `ontologies/agent.ttl` → `docs/agent.md`
- `ontologies/ai-agent.ttl` → `docs/ai-agent.md`
- `ontologies/ethereum.ttl` → `docs/ethereum.md`
- `ontologies/ethereum-intent-delegation.ttl` → `docs/ethereum-intent-delegation.md`
- `ontologies/agent-profile.ttl` → `docs/agent-profile.md`
- `ontologies/identity.ttl` → `docs/identity.md`
- `ontologies/capability.ttl` → `docs/capability.md`
- `ontologies/delegation.ttl` → `docs/delegation.md`
- `ontologies/intent.ttl` → `docs/intent.md`
- `ontologies/execution-context.ttl` → `docs/execution-context.md`
- `ontologies/ledger.ttl` → `docs/ledger.md`
- `ontologies/accountability.ttl` → `docs/accountability.md`
- `ontologies/security-binding.ttl` → `docs/security-binding.md`
- `ontologies/threat-model.ttl` → `docs/threat-model.md`
- `ontologies/payment.ttl` → `docs/payment.md`
- `ontologies/economic.ttl` → `docs/economic.md`
- `ontologies/contract.ttl` → `docs/contract.md`
- `ontologies/lifecycle.ttl` → `docs/lifecycle.md`
- `ontologies/ontic-alignment.ttl` → `docs/ontic-alignment.md`

## Conventions used in examples

Examples are written in the **Agent Trust Graph** domain (Global.Church / ENS / ERC-8004), using:

- **This repo’s terms** (e.g., `core:Agent`, `core:hasCapability`, `del:Delegation`, `ledger:LedgerEvent`)
- **A domain extension namespace** `gc:` for Global.Church-specific classes/properties (e.g., endorsements, org types, memberships)
- **External standards** where helpful (`prov:`, `skos:`), to match the design reference

The intent is to show how to *instantiate this ontology* as the “meaning layer”, while letting the Global.Church domain add its own trust-graph edges.


# Unique extensions beyond PROV-O / P-PLAN / EP-PLAN

This repo **imports and aligns** to:

- **PROV-O** (`prov:`): provenance primitives (Agent/Activity/Entity/Plan) and delegation (`prov:actedOnBehalfOf`, `prov:Delegation`).
- **P-PLAN** (`pplan:`): plan decomposition primitives (Plan/Step/Variable) and their correspondence to executions.
- **EP-PLAN** (`epplan:`): plan + execution trace alignment, including constraints/objectives/communications.

The core value of this ontology is **not** re-stating those standards; it is the **agent-systems-specific layer** that they intentionally do not provide.

## What’s unique in this ontology

### Capability semantics for LLM + executable systems

Upper ontologies don’t provide a capability/skill model. This repo adds a split between:

- **Semantic capability** (`cap:Capability`): natural-language / fuzzy-matchable “what I can do”
- **Executable skill** (`cap:Skill`): atomic operations with explicit parameters (`cap:Parameter`)

In practice, this gives you a **two-level interface**:

- **Discovery / matching layer**: you can search across agents using `cap:capabilityExpression` (keywords / NL descriptors) and `cap:capabilityConfidence` (self-assessed ability).
- **Execution wiring layer**: once matched, you can traverse `cap:hasSkill` to the executable building blocks and their parameter model (`cap:hasParameter`, `cap:parameterName`, `cap:parameterType`).
- **Policy gating layer**: `cap:CapabilityConstraint` can express allowed/prohibited contexts and permissions required (`cap:allowedContext`, `cap:prohibitedContext`, `cap:requiresPermission`), plus lineage (`cap:CapabilityProvenance`, `cap:sourceSpec`, `cap:capabilityVersion`).

Primary module:

- `ontologies/capability.ttl`

### Payment intent + settlement interfaces (value transfer as intent)

Upper ontologies don’t define a normative vocabulary for *value transfer* between agents. This repo adds:

- `payment:PaymentIntent ⊑ core:Intent` (value transfer as a first-class intent type)
- `payment:SettlementInterface` (how a payment can be settled: method/category/etc.)
- payer/payee/amount/unit/reason bindings on the intent

In practice, this lets you:

- **express value-transfer as an actionable goal** that can be authorized via delegation (because it is a `core:Intent`),
- **normalize payment IO** across systems (fiat rails, stablecoins, on-chain transfers) via `payment:SettlementInterface` metadata,
- and carry human/audit context (`payment:reason`) alongside amounts/units.

Primary module:

- `ontologies/payment.ttl`

### Delegation as a scoped permission envelope (not just a binary “on behalf of”)

PROV can express *that* one agent acts for another, but not *what they are allowed to do*, under what intent boundary, and in what time window.

This repo models:

- `del:Delegation` as a reified authority transfer aligned to `prov:Delegation`
- `del:DelegationScope` as a **machine-checkable permission boundary**
  - capability constraints (`del:allowedCapability`)
  - intent constraints (`del:intentConstraint`)
  - temporal validity (`del:validFrom`, `del:validUntil`)
  - operational constraints (`del:ExecutionConstraint`)

And it provides a PROV-compatible view of delegation via a property-chain entailment:

- `del:actedOnBehalfOf ⊑ prov:actedOnBehalfOf`

In practice, this is the core “policy surface” for multi-agent systems:

- **what** the delegate can do (capabilities),
- **why** they can do it (intent boundary),
- **when** they can do it (time window),
- **under what operational constraints** (execution constraints),
- with optional traceability hooks (`del:recordedIn`, `del:accountableFor`) and chain structure (`del:DelegationChain`).

Primary module:

- `ontologies/delegation.ttl`

### Execution context, security binding, and resource constraints for real deployments

Upper ontologies are deliberately abstract. This repo adds the “runtime envelope” needed to audit and govern real agent executions:

- execution environment, security context, input/output state capture (`ontologies/execution-context.ttl`)
- security binding from identity → keys → hardware root of trust → ledger verification (`ontologies/security-binding.ttl`)
- resource constraints like budgets/rate limits (execution-context)
- privacy/data handling and access control rules bound to `core:ExecutionContext` (security-binding)

In practice, these modules are where “agent semantics” becomes **deployable**:

- `execution-context.ttl` lets you describe the environment envelope (sandbox/container/TEE), security posture, and IO snapshots as first-class entities, plus compute/financial constraints (rate limits, budgets).
- `security-binding.ttl` lets you describe *why to trust* identity and execution evidence by binding DID/identity material to cryptographic keys and hardware roots of trust, and then to ledger verification records.
- security-binding also provides a vocabulary for privacy/access control policies attached to an execution context (e.g., encryption required, allowed actions, restricted roles).

Primary modules:

- `ontologies/execution-context.ttl`
- `ontologies/security-binding.ttl`

### Ledger + accountability as first-class audit objects

PROV is a generic provenance model. This repo adds opinionated, audit-friendly structures:

- **append-only ledger events** with hashes/signatures and governance vs runtime distinction (`ontologies/ledger.ttl`)
- **accountability events** for responsibility attribution and compliance assessment (`ontologies/accountability.ttl`)

It also adds explicit “agent-runtime” linkages that upper ontologies leave to profiles/patterns, such as:

- binding an `intent:Intent` to a delegation that authorizes it (`intent:authorizedBy`)
- binding a `core:Action` to an execution context (`intent:executedIn`)
- binding a `core:Action` to a ledger record (`intent:loggedAs`)

In practice, you get two different artifacts:

- **Ledger events** answer “what happened” with integrity guarantees (hashes, signatures, append-only linkage) and strong bindings to actors, delegations, contexts, capabilities, and results.
- **Accountability events** answer “was it acceptable/authorized/compliant” with explicit responsibility attribution, delegation basis, and compliance assessments (e.g., AI Act hooks).

Primary modules:

- `ontologies/ledger.ttl`
- `ontologies/accountability.ttl`

### “Agent” as an operational system (profiles, IO, dependencies, lifecycle, economics, contracts)

PROV’s `prov:SoftwareAgent` is intentionally minimal. This repo defines the agent as something you can *deploy, integrate, and govern*:

- profiles (`ontologies/agent-profile.ttl`)
- identity bindings (`ontologies/identity.ttl`)
- operational triggers + IO specs, dependency graph (`ontologies/agent.ttl`)
- lifecycle state (`ontologies/lifecycle.ttl`)
- wallets/payments/economic interfaces (`ontologies/economic.ttl`, `ontologies/payment.ttl`)
- contractual interfaces (`ontologies/contract.ttl`)

In practice, `agent.ttl` is the “deployment contract” for an agent:

- **purpose/ownership** (`agent:PurposeRole`, `agent:objective`, `agent:owner`)
- **operational IO** (triggers, input/output specs)
- **dependency graph** (`agent:SystemIntegration`, `agent:dependsOn`, interface specs)
- **interfaces** into identity, economics, lifecycle, and contract modules
- and it still respects PROV-O’s agent taxonomy by aligning `agent:SoftwareAgent`, `agent:Person`, `agent:Organization` to PROV-O.

Primary modules:

- `ontologies/agent.ttl`
- plus the supporting modules listed above

### Agent profiles (pragmatics/behavior) as a stable descriptor

Upper ontologies intentionally avoid “behavioral style”. This repo adds a pragmatic layer describing *how* an agent communicates/behaves (personality, language preference, tone model) as a first-class artifact for coordination stability.

In practice, this is where you encode stable “interaction knobs” that change outcomes in multi-agent systems:

- preferred language/register,
- tone/cadence/assertiveness,
- and persistent behavioral bias patterns (personality-like descriptors).

Primary module:

- `ontologies/agent-profile.ttl`

### Contracts (linking agents to explicit terms)

Upper ontologies don’t provide a dedicated contract/terms vocabulary. This repo adds a minimal contract set:

- `contract:ContractSet` containing `contract:Contract`
- `contract:termsUri` as a machine-resolvable pointer to terms

In practice, this provides a minimal, interoperable way to say:

- “this agent is operating under these terms,” and
- “here is the canonical URI where those terms live.”

Primary module:

- `ontologies/contract.ttl`

### Lifecycle (genesis/health/decommission) for deployed agents

Upper ontologies can represent provenance of lifecycle events, but they don’t give you a shared vocabulary for agent lifecycle state. This repo adds:

- `life:LifecycleRecord` with genesis/health-check/decommission records and a lifecycle state

In practice, this lets you standardize lifecycle reporting across deployments:

- initial provenance / genesis assertions,
- health check specification hooks,
- and decommissioning metadata for audit and governance.

Primary module:

- `ontologies/lifecycle.ttl`

### AI Agent as a specialization of software agent + software application/service

PROV-O gives you `prov:SoftwareAgent`, but no explicit “AI agent” class and no bridge to software catalog vocabularies. This repo adds:

- `ai:AIAgent ⊑ core:Agent` and `⊑ prov:SoftwareAgent`
- plus interoperability with `schema:SoftwareApplication` and `schema:Service`

In practice, this gives you a clean “AI agent” class that is:

- semantically compatible with provenance (`prov:SoftwareAgent`),
- compatible with software catalogs/registries (`schema:SoftwareApplication`, `schema:Service`),
- and still inherits the repo’s core agent semantics (`core:Agent`).

Primary module:

- `ontologies/ai-agent.ttl`

### Domain overlays (example: Ethereum execution + delegation standards)

Upper ontologies don’t model blockchain execution primitives. This repo adds an **Ethereum overlay** that maps intents and delegations to:

- EIP-7702 set-code authorization tuples
- ERC-7710 delegation redemption contexts (MetaMask framework patterns)
- ERC-4337 account abstraction execution (`UserOperation`, `EntryPoint`, bundler/paymaster roles)

In practice, this overlay lets you bind abstract agent semantics to concrete on-chain artifacts:

- represent EOA “set-code” authorization tuples (EIP-7702),
- represent delegation redemption contexts and managers (ERC-7710 patterns),
- represent account-abstraction execution objects (ERC-4337),
- and connect `intent:Intent` / `core:Action` / `del:Delegation` to those artifacts for auditability.

Primary modules:

- `ontologies/ethereum.ttl`
- `ontologies/ethereum-intent-delegation.ttl`

### Minimal threat-model interoperability layer

This repo includes a simplified ontology for threat modeling / minimal interoperability for agent-to-agent security reasoning (capability misrepresentation, intent ambiguity, delegation threats).

Primary module:

- `ontologies/threat-model.ttl`

## When to use which (quick guide)

- **Need to find an agent that can do X**: start with `cap:Capability` / `cap:capabilityExpression` and then traverse to `cap:Skill`.
- **Need to authorize an agent to do X safely**: model a `del:Delegation` + `del:DelegationScope` and bind it to the `intent:Intent` via `intent:authorizedBy`.
- **Need to describe runtime constraints (TEE/sandbox/budgets/rate limits)**: use `core:ExecutionContext` + `ctx:*` and attach security policies with `sec:*`.
- **Need immutable audit trails**: write `ledger:LedgerEvent` records; then produce `acc:AccountabilityEvent` assessments for governance/compliance.
- **Need money movement**: express it as `payment:PaymentIntent` and specify settlement requirements.

## Where the upper ontologies show up (alignment “spine”)

If you want to see the alignment points in one place, start here:

- `ontologies/core.ttl`:
  - `core:Agent ⊑ prov:Agent`
  - `core:Action ⊑ prov:Activity` and `⊑ pplan:Activity` and `⊑ epplan:Activity`
  - `core:Intent ⊑ prov:Plan` and `⊑ pplan:Plan` and `⊑ epplan:Plan`
  - `core:Task ⊑ pplan:Step` and `⊑ epplan:Step`
- `ontologies/intent.ttl`:
  - intent parameters aligned to plan variables (`pplan:Variable`, `epplan:Variable`)
- `ontologies/delegation.ttl`:
  - delegation aligned to `prov:Delegation`, plus `prov:actedOnBehalfOf` view


## Lifecycle Ontology (`ontologies/lifecycle.ttl`)

Defines a minimal lifecycle record for agents: genesis record, health checks, state, and decommission record.

### Key terms

- **Classes**
  - `life:LifecycleRecord`
  - `life:GenesisRecord`
  - `life:HealthCheckSpec`
  - `life:DecommissionRecord`
- **Object properties**
  - `life:genesisRecord` (LifecycleRecord → GenesisRecord)
  - `life:healthCheck` (LifecycleRecord → HealthCheckSpec)
  - `life:decommissionRecord` (LifecycleRecord → DecommissionRecord)
- **Datatype properties**
  - `life:state` (LifecycleRecord → string)

### Hierarchy diagram

```mermaid
classDiagram
  class lr["life:LifecycleRecord"]
  class g["life:GenesisRecord"]
  class h["life:HealthCheckSpec"]
  class d["life:DecommissionRecord"]

  lr --> g : genesisRecord
  lr --> h : healthCheck
  lr --> d : decommissionRecord
```

### Relationship diagram

```mermaid
graph TD
  A[core:Agent] -->|agent:lifecycleManagement| LR[life:LifecycleRecord]
  LR -->|life:state| S[(state)]
  LR -->|life:genesisRecord| G[life:GenesisRecord]
  LR -->|life:healthCheck| H[life:HealthCheckSpec]
  LR -->|life:decommissionRecord| D[life:DecommissionRecord]
```

### SPARQL queries

```sparql
PREFIX agent: <https://w3id.org/agent-ontology/agent#>
PREFIX life: <https://w3id.org/agent-ontology/lifecycle#>

# 1) Agents and lifecycle states
SELECT ?agent ?state WHERE {
  ?agent agent:lifecycleManagement ?lr .
  OPTIONAL { ?lr life:state ?state . }
}
```

```sparql
PREFIX life: <https://w3id.org/agent-ontology/lifecycle#>

# 2) Decommissioned agents (has decommission record)
SELECT ?lr ?decom WHERE {
  ?lr a life:LifecycleRecord ;
      life:decommissionRecord ?decom .
}
```

### Example (Agent Trust Graph domain)

```turtle
@prefix agent: <https://w3id.org/agent-ontology/agent#> .
@prefix life: <https://w3id.org/agent-ontology/lifecycle#> .
@prefix gc: <https://global.church/ontology/gc#> .

gc:lausanne-gca-eth a agent:Organization ;
  agent:lifecycleManagement [
    a life:LifecycleRecord ;
    life:state "active" ;
    life:genesisRecord [ a life:GenesisRecord ] ;
    life:healthCheck [ a life:HealthCheckSpec ]
  ] .
```


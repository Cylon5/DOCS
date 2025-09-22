## General Architecture

This diagram shows the "bottom-up" process where a local operator proposes a new configuration. The proposal is sent to the central Reconciler, which validates it against the source of truth (DB). If accepted, the Reconciler promotes it to the new "desired" state, triggering the top-down flow.

```puml
@startuml C4_Config_Node_To_Central
!include <C4/C4_Container>

LAYOUT_WITH_LEGEND()

title C4 Container Diagram: Node to Central Configuration Proposal Flow

Person(local_admin, "Local DAQ Operator", "Can propose new configurations locally.")

System_Boundary(central, "Central System") {
    Container(api, "Management API", "Provides endpoints to manage the canonical configuration.")
    ContainerDb(postgres, "PostgreSQL Database/SQLite", "SQL Database", "The permanent 'source of truth' for all configurations.")
    Container(reconciler, "Reconciler Service", "The bridge that syncs the source of truth with the real-time distribution layer.")
    Container(nats, "NATS", "Messaging, KV & Object Store", "The real-time layer for distributing config and reporting state.")
}

System_Boundary(edge, "Edge System") {
    Container(daq_node, "DAQ Application", "Applies desired configuration and can propose new configurations.")
}

' Bottom-up flow (Node -> Central)
Rel(local_admin, daq_node, "A. Proposes new config via")
Rel(daq_node, nats, "B. Publishes 'proposed' state", "NATS")
Rel(nats, reconciler, "C. Watches for proposals", "NATS Watch")
Rel(reconciler, postgres, "D. Validates proposal against source of truth", "SQL")
Rel(reconciler, postgres, "E. Writes accepted proposal to source of truth", "SQL")
@enduml
@enduml
```

## Sequence Diagram

This flow enables controlled, auditable configuration changes to be initiated from the edge.

- A local operator submits a **proposed state** through the local DAQ Application.
- The `DAQ Application` publishes this proposed configuration to the `NATS` platform.
- The central `Reconciler Service`, which monitors for such proposals, consumes the message.
- The Reconciler performs validation logic, comparing the proposal against the current source of truth in `PostgreSQL`.
- If the proposal is deemed valid, the `Reconciler writes` the changes to the PostgreSQL database. This act ratifies the proposal, atomically updating the canonical source of truth.
- This update subsequently triggers the top-down propagation flow, ensuring the newly accepted configuration is consistently distributed across all relevant edge systems.

```mermaid
sequenceDiagram
    title "Node-to-Central Configuration Proposal Flow"
    autonumber

    participant LocalAdmin as "Local Admin"
    participant N as "Node"
    participant KV as "KV Store (NATS)"
    participant R as "Reconciler Service"
    participant DB as "Source of Truth (DB)"

    LocalAdmin->>N: 1. Proposes new configuration
    N->>KV: 2. Puts cfg.proposed.<node> state
    note left of N: Publishes proposed state

    R-->>KV: 3. Watches cfg.proposed.<node> for proposals
    R->>DB: 4. Validates proposal against source of truth
    DB-->>R: 5. Validation result

    alt Proposal is valid
        R->>DB: 6. Writes accepted proposal to DB
        note right of R: Proposal becomes the new source of truth
        R->>KV: 7. Puts cfg.desired.<node> metadata
        note right of R: Triggers the Central-to-Node flow
    else Proposal is invalid
        R->>KV: 8. Puts cfg.reported.<node> {status: 'proposal_rejected'}
        note right of R: Reports rejection status
    end
```
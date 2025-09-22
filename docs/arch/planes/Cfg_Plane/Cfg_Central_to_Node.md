## General Architecture

This diagram shows the top-down flow where an administrator defines a configuration that is pushed to the edge DAQ application.

```puml
@startuml C4_Config_Central_To_Node
!include <C4/C4_Container>

LAYOUT_WITH_LEGEND()

title C4 Container Diagram: Central to Node Configuration Flow

Person(admin, "DAQ Operator / Admin", "Defines the 'desired' configuration state centrally.")

System_Boundary(central, "Central System") {
    Container(api, "Management API", "Provides endpoints to manage the canonical configuration.")
    ContainerDb(postgres, "PostgreSQL Database/SQLite", "SQL Database", "The permanent 'source of truth' for all configurations.")
    Container(reconciler, "Reconciler Service", "The bridge that syncs the source of truth with the real-time distribution layer.")
    Container(nats, "NATS", "Messaging, KV & Object Store", "The real-time layer for distributing config and reporting state.")
}

System_Boundary(edge, "Edge System") {
    Container(daq_node, "DAQ Application", "Applies desired configuration and can propose new configurations.")
}

' Top-down flow (Central -> Node)
Rel(admin, api, "1. Defines desired config via")
Rel(api, postgres, "2. Writes to source of truth", "SQL")
Rel(postgres, reconciler, "3. Notifies of change", "CDC / API Call")
Rel(reconciler, nats, "4. Publishes 'desired' state", "NATS")
Rel(nats, daq_node, "5. Watches for and applies desired state", "NATS Watch")
Rel(daq_node, nats, "6. Reports 'applied' state", "NATS")
@enduml
```

## Sequence Diagram

This flow describes the proposed method for distributing configuration.

- 	An administrator defines the **desired state** of the system configuration via a `Management API`.

- 	The API transactionally commits this configuration to a persistent datastore (`PostgreSQL`), which serves as the "source of truth".
	
- A change to the database triggers the `Reconciler Service`, either through a Change Data Capture (CDC) mechanism or a direct API call.

- The `Reconciler Service` then publishes this desired state to the NATS messaging and data-streaming platform.

- The `DAQ Application` at the edge, acting as a client, subscribes to these updates, retrieves the new configuration, and applies it to its local environment.

- Finally, the `DAQ Application` provides a feedback loop by reporting its applied state back through NATS, confirming the successful application of the configuration.  


```mermaid
sequenceDiagram
    title "Central-to-Node Configuration Flow"
    autonumber

    participant Admin
    participant API
    participant DB as "Source of Truth (DB)"
    participant R as "Reconciler Service"
    participant KV as "KV Store (NATS)"
    participant OBJ as "Object Store (NATS)"
    participant N as "Node"

    Admin->>API: 1. Submits new configuration
    API->>DB: 2. Writes to source of truth
    
    DB-->>R: 3. Notifies of change (e.g., CDC event)
    note right of R: Reconciler is triggered by the DB update
    
    R->>OBJ: 4. Puts config blob
    R->>KV: 5. Puts cfg.desired.<node> metadata
    note right of R: Publishes desired state to NATS

    N-->>KV: 6. Watches cfg.desired.<node>
    N->>OBJ: 7. Fetches config blob
    N->>N: 8. Verifies and applies config
    N->>KV: 9. Puts cfg.reported.<node> status
    note left of N: Reports its applied state

    R-->>KV: 10. Watches cfg.reported.<node>
    note right of R: Observes applied state to confirm convergence
```
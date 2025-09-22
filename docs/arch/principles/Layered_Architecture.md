The system will be organized into layers,and is built on the principle of decoupling producers from consumers.

## Distributed Nodes Layer

### DAQ Nodes
   - Collects sensor data via OPC, Modbus, VISA, DAQMx, MQTT, or custom drivers.
   - Stores native high-resolution data locally using TDMS.
   - Publishes down-sampled/aggregated data to NATS JetStream subjects (e.g., `JS_MEASUREMENTS.ch.<channel_id>.<agg>.<window_ms>`).
   - Subscribes to control/config messages via NATS (e.g., `CONTROL.<site>.<node>.device.<device_id>.*` or. `CONTROL.<site>.*`)
   
### Processing Nodes - Stream processors

- Multiple consumers/producers and/or stream processors performing analytics or other data transformation.

### Workers
-  Windows services that perform TDMS consolidation over SFTP/SSH using **`rclone`**.
- **TSDB + Grafana/REST.** Optional/dotted component. Ingests **measurements** and **processed** streams.  
 
 
## Central Orchestration and Logging Layer

- Three NATS servers with JetStream (replicas=3).Optionally host a leafnode (recommended for outage buffering from the Main hub)
- NATS JetStream broker manages subject streams, message retention, and replay.
- Time-series (TBD) ingests structured sensor data for fast querying and dashboards.
- Relational database (TBD) is used a 'source of truth' for configuration data, session metadata
- Object and Key-Value stores in JetStream are used to cache:
     - TDMS file references
     - DAQ Nodes configuration states
     - Session metadata

## Visualization & Command Layer

   - Real-time Web Application dashboards (e.g., using WebSockets) receive updates under 1s latency.
   - DSE engineers issue control messages via UI/API → NATS REQ/REP to edge nodes.
   - Backend services can replay data, trigger anomaly detection, or execute processing flows.
     
     

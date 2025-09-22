- **Publish Grouped Data:** the DAQ node publishes the efficient, time-correlated **grouped messages** to a subject like `measurements.preprocessed.site.node.device.<device_id>`.

- Use a **"Demultiplexer" Service:** create a small, dedicated NATS service that subscribes to the grouped stream (`measurements.preprocessed....>`). Its only job is to receive each grouped message, break it apart, and republish the data as **per-channel messages** on subjects like `measurements.ch.<channel_id>.map.<map_version>`.

This gives all the potential consumers a choice.

- **Heavy-duty consumers** like an ingestion service subscribe to the efficient `measurements.preprocessed...` stream.

- **Lightweight consumers** like a single UI chart or a simple alert subscribe to the granular `measurements.ch...` stream.

```mermaid
graph TD
    A[DAQ Node] -- "1. Publishes grouped message" --> B(NATS);

    subgraph "High-Performance Consumers"
        B -- "subject: measurements.preprocessed.device.123.map.mapv3" --> C[Ingestion Service/Processing Nodes];
    end

    subgraph "Flexible, Low-Volume Consumers"
        B -- "subject: measurements.preprocessed.device.123.map.mapv3" --> D["Demultiplexer Service"];
        D -- "2. Splits and republishes to new subjects" --> B;
        B -- "subject: measurements.ch.a1b2c3d4-e5f6-4a5b-8c9d-0123456789ab.avg.1000ms" --> E["Real-time UI Chart"];
        B -- "subject: measurements.ch.b2c3d4e5-f6a7-4b5c-9d8e-123456789abc.max.100ms" --> F["Alerting Service"];
    end

    %% This invisible link forces the bottom subgraph to be rendered below the top one.
    C ---> D;
```



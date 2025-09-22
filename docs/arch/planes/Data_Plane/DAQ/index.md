#DAQ Producer Node

In this architecture, the main purveyor of  data is the DAQ Node. It:  

   - Collects sensor data via OPC, Modbus, VISA, DAQMx, MQTT, or custom drivers.
   - Stores native high-resolution data locally using TDMS.
   - Publishes down-sampled/aggregated data to NATS JetStream subjects (e.g., `JS_MEASUREMENTS.ch.<channel_id>.<metric>.<window_ms>`).
   - Subscribes to control/config messages via NATS (e.g., `CONTROL.<site>.<node>.device.<device_id>.*` or. `CONTROL.<site>.*`)
   
   
```puml
@startuml
' Diagram Title
title Component Diagram for Data Acquisition (Data vs. Control)

' Use a theme for better visuals
!theme mars

' Define Components
[Sensor] as sensor
[DAQ] as dataCollector
[Control Handling] as controlHandler
database "TDMS Local Storage" as tdms
queue "NATS Data Stream (JetStream)" as natsData
queue "NATS Control Bus" as natsControl

' Define Relationships and Data Flow for Data Plane
sensor --> dataCollector : Sends raw sensor data
dataCollector --> tdms : Stores native high-resolution data
dataCollector .> natsData : Publishes down-sampled/aggregated data to\n'JS_MEASUREMENTS.ch...<NATS_SUBJECT>'

' Define Relationships and Data Flow for Control Plane
natsControl .> controlHandler : Receives control messages from\n'CONTROL.<site>...<NATS_SUBJECT>'
controlHandler --> dataCollector : Applies configuration/commands

@enduml
```

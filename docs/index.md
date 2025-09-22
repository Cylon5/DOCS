#Background

Industrial and sensor-heavy environments require the capture and analysis of high-frequency, heterogeneous data streams from multiple DAQ nodes. 
These DAQ nodes interface with a wide range of Industrial protocols or low level device drivers such as OPC, Modbus, VISA, DAQMx, MQTT amongst others,
and collect data from devices operating at vastly different sampling rates—from low-rate environmental sensors to high-rate instruments like high-speed cameras or DAQ systems.

Given the constraints of bandwidth, data variety, and need for resilience in intermittent connectivity environments, DAQ nodes must retain high-resolution data locally using structured file formats such as TDMS. Meanwhile, for centralized visibility, processing, and long-term analysis, the DAQ nodes also stream summarized or transformed data in near real-time using the NATS Core and NATS JetStream messaging technology.

The proposed architecture must enable integration of raw nodes-collected data with centrally stored data to support auditability, diagnostics, compliance, and post-analysis, while also enabling real-time or near-real-time insights from intermediate processing nodes.
Additionally, there is a requirement for real-time visualization of selected data streams to enable human operators and automated systems to react promptly to key metrics, anomalies, or system states.

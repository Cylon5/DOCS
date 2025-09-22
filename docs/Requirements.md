## General Requirements

The proposed platform must provide:

* **Reliable Data Ingestion:** The system must dependably collect (ingest) data that has already been cleaned, filtered, or calculated (curated and/or processed). This ensures that the central database isn't filled with raw, noisy, or irrelevant information.

* **Low-Latency Access:** Users and other applications must be able to retrieve data with minimal delay (low-latency). This is critical for real-time monitoring dashboards, alarm systems, and control loops where immediate access to current data is essential.

* **Precise Data Replay:** The system is built to guarantee data integrity and historical accuracy. By using references to the original Technical Data Management Streaming (TDMS) files, it can perfectly reconstruct and replay the state of the system at any point in time. This is invaluable for troubleshooting failures, analyzing past events, or validating models.

* **Centralized Data Management:** Instead of data being scattered across many different machines, all TDMS files are gathered and stored in a single, central location. This consolidation simplifies backups, enhances security, and provides a "single source of truth" for all analysis.

* **Interactive Control:** This is not a one-way system. It supports command and control, meaning operators and or engineers can send instructions from the central system to the Nodes oa a node can be configured locally. The bidirectional configuration allows users to both read the current settings of a device (e.g., a sensor's sample rate) and write new settings to it.

## Operating Environment

This section outlines the technical and physical constraints under which the system is designed to operate.

* **Software Platform:** The system is primarily developed for Windows operating systems, which will be used on both the main central servers and the remote data acquisition (DAQ) machines connected to sensors. However, it should not exclude providing an optional variant that can run within a Linux system or Linux Virtual Machine (VM) if required.

* **Network Resilience:** A key design assumption is that the Local Area Network (LAN) can be unreliable and can experience intermittent outages.

* **Zero-Data-Loss Strategy:** To function through network outages, each remote machine (node) has a local storage buffer. It continuously saves data locally into TDMS files and can hold between 7 to 30 days of data (TBD). When the network connection is down, the node operates autonomously. Once the network is restored, it automatically synchronizes its locally-stored data with the central server, ensuring no data is ever lost. This is a classic "store-and-forward" architecture.

## Timebase

This describes how the system ensures all data can be accurately correlated across different machines and sensors.

* **Synchronized Clocks:** Across the entire facility (site-wide), all computers and devices have their internal clocks synchronized using NTP (Network Time Protocol),the more precise PTP (Precision Time Protocol) or other protocols such as IRIG if required.

* **Comparable Timestamps:** The result of this synchronization is that a timestamp of 10:45:20.123 from a sensor in one building means the exact same moment in time as a timestamp of 10:45:20.123 from a controller in another building. This is absolutely critical for correctly analyzing cause-and-effect relationships between different parts of the facility. Without it, you could never be sure if a pressure spike truly happened before, during, or after a temperature drop

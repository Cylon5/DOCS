# Logical Planes of Operations

The messaging system in the proposed architecture operates on three distinct logical planes, each with a specific purpose, data format, and set of participants.  

## Data Plane 

This plane is responsible for the real-time transport of sensor and measurement data from the DAQ nodes to the central system. It is split into two streams to balance efficiency and flexibility.

Its main purpose is to efficiently transport large volumes of raw or pre-processed data with minimal network overhead and or provide a simple, granular stream for lightweight consumers that only need data from selected channels.

## Configuration Plane

This plane is responsible for distributing configuration to the edge and reconciling the state between the central system and the all the nodes. It is a command-and-control layer.

Its main purpose is to ensure distributed nodes are running the correct configuration and to allow them to propose changes in a controlled manner.

## Management Plane

This plane acts as a real-time notification bus, informing services of changes made to the "source of truth" in the PostgreSQL database.

Its main purpose is to keep the distributed, in-memory caches (used by the Extract Tranform Load, API, Distributed Nodes etc.) synchronized with the "source of truth" metadata without needing to poll the database.
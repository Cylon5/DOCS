# Change Data Capture (CDC)

Change Data Capture (CDC) is a design pattern that aims to solve the problem of tracking data changes in a source/master system and distributing those changes to other systems. In traditional scenarios, synchronizing systems with database changes posed challenges.

CDC addresses this by continuously monitoring databases for changes and capturing them as they happen. 

Rather than periodically polling the entire database, CDC systems utilize transaction logs or similar mechanisms to capture only altered data, minimizing processing overhead and ensuring immediate synchronization.


## Debezium Server

Debezium is an open-source platform for Change Data Capture (CDC). It monitors databases and captures row-level changes, emitting them as event streams. When integrated with PostgreSQL, Debezium captures every change made to the database in real time. 

Debezium Server is a standalone application. It's designed to stream change data capture (CDC) events directly from a source database to a variety of messaging platforms.It was introduced later as an alternative packaging — so you could use Debezium connectors without needing Kafka or Kafka Connect

![image1](assets\img\CDC_DEBEZIUM_NATS.png)  

```embed
url:            https://dev.to/bobur/data-synchronization-in-microservices-with-postgresql-debezium-and-nats-a-practical-guide-ck9
name:           Data Synchronization in Microservices with PostgreSQL, Debezium, and NATS: A Practical Guide 
desc:           In modern software development, the microservices architecture has become popular for its scalability and flexibility. Despite its benefits, it brings significant challenges, particularly in data synchronization across various services. By leveraging PostgreSQL, Debezium, and NATS, we can establish an efficient and reliable method for synchronizing data across microservices.
image:          https://media2.dev.to/dynamic/image/quality=100/https://dev-to-uploads.s3.amazonaws.com/uploads/logos/resized_logo_UQww2soKuUsjaOGNB38o.png
favicon:        https://media2.dev.to/dynamic/image/quality=100/https://dev-to-uploads.s3.amazonaws.com/uploads/logos/resized_logo_UQww2soKuUsjaOGNB38o.png
favicon_size:   25
target:         new
accent:         4C59BFE0
```

```embed
url:            https://natsbyexample.com/examples/integrations/debezium/cli#recording
name:           Change Data Capture via Debezium 
desc:           Change data capture (CDC) can be desirable to setup on databases in order to stream granular change events to other parts of the system as data changes. A fairly popular open source project that handles the intricacies of interfacing with a variety of databases to capture changes is Debezium.
image:          https://natsbyexample.com/nats-horizontal-color.svg
favicon:        https://natsbyexample.com/nats-horizontal-color.svg
favicon_size:   25
target:         new
accent:         4C59BFE0
```


!!! warning "SQLite usage a configuration DB"

    SQLite is still a valid candidate for configuration database. However note that main CDC Tools available (Open-source or commercial) **do not** support SQLite. The CDC design pattern would have to be implemented "manually"

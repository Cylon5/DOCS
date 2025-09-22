As documented in the [Architecture Decisions Records](../../ADR) NATS was chosen as the messaging system for this project:

```embed
url:            https://docs.nats.io/nats-concepts/overview
name:           Overview | NATS Docs
desc:           NATS is a connective technology that powers modern distributed systems. A connective technology is responsible for addressing, discovery and exchanging of messages that drive the common patterns in distributed systems; asking and answering questions, aka services/microservices, and making and processing statements, or stream processing.
image:          https://hub.docker.com/api/media/repos_logo/v1/library%2Fnats?type=logo
favicon:        https://github.com/Aetherinox/mkdocs-link-embeds/assets/118329232/13a151b1-d7f9-4e27-909b-a26986ab0954
favicon_size:   25
target:         new
accent:         4C59BFE0
```

## NATS Subjects

At its simplest, a subject is just a string of characters that form a name the publisher and subscriber can use to find each other. More commonly subject hierarchies are used to scope messages into semantic namespaces

## Hierarchical Structure:
Subjects are structured hierarchically using dots (`.`) as delimiters. This allows for the logical organization of message streams. For example, `JS_MEASUREMENTS.ch` and `JS_MEASUREMENTS.Node` are two distinct subjects within the `JS_MEASUREMENTS` hierarchy.

+ Wildcards for Flexible Subscriptions: Subscribers can use wildcards to listen to multiple subjects at once. This is a powerful feature for consuming related streams of data. There are two types of wildcards:
	+ **`*`(asterisk)**: Matches a single token in the subject. For instance, a subscription to `JS_MEASUREMENTS.*` would receive messages published to `JS_MEASUREMENTS.ch` and `JS_MEASUREMENTS.Node`, but not `JS_MEASUREMENTS.Node.Device`.

	+ **`>` (greater than):** Matches one or more tokens at the end of a subject. A subscription to `JS_MEASUREMENTS.>` would receive messages on `JS_MEASUREMENTS.ch`, `JS_MEASUREMENTS.Node`, and `JS_MEASUREMENTS.Node.Device`.

+ **Role in Routing and Filtering:** The NATS server uses subjects to efficiently route messages from publishers to the appropriate subscribers. This subject-based filtering happens at the server level, minimizing unnecessary network traffic and processing for clients. The hierarchical nature and wildcard support enable fine-grained control over which messages a subscriber receives.
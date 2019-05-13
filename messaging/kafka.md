# Kafka

 Kafka’s performance comes primarily from two design choices:
 
 - The broker does not manage consumer state, each consumer does. This reduces the load on the broker, but also results in an inability 
 for DevOps to monitor consumer behavior, deal with the “message of death”, be sure of message delivery, provide wildcard subscriptions and 
 requires more complexity in each client API and application.

- The Apache Kafka broker’s use of the ***sendfile() system call*** to transfer messages to consumers is key to its performance, 
but it can only be used when consumers receive messages exactly as they are stored to disk – transport protocol and all. As a result, 
sendfile() cannot be used when TLS is used (resulting in 90% performance decrease) or where backward compatibility is required or if 
any filtering or modification is required to messages after they are stored. In these cases Apache Kafka loses its performance advantage.

### Resources
1. [Solace](https://solace.com/differences/kafka/datapath-architecture/)

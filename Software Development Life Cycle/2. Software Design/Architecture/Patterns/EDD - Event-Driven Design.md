# EDD - Event-Driven Design

Source: []()

Tags: **[]**

# Index

- [**What is?**](#whats-it)
- [**Main applications**](#main-applications)
- [**Advantages**](#advantages)
- [**Disadvantages**](#disadvantages)
- [**Inner function**](#inner-function)

# What´s it?

Event-Driven Design (EDD) is a software architecture approach where systems react to events—changes in state or user actions—rather than following a fixed, sequential flow. It enables real-time responsiveness, scalability, and loose coupling between components.

- Event: Any significant occurrence in the system (e.g., a user clicks a button, a payment is processed, a sensor sends data).
- Producers (Emitters): Components that generate events.
- Consumers (Subscribers): Components that listen for and react to events.
- Event Broker/Bus: Middleware that routes events to the right consumers.

Instead of tightly coupling services, each reacts independently when an event happens.

# Main Applications

- E-commerce: Order triggers payment, shipping, and notification services.
- Banking: Fraud detection reacts instantly to suspicious transactions.
- Gaming: Multiplayer state updates broadcast events to all players.
- IoT: Smart home devices publish sensor data, triggering automation rules.

# Advantages

- Loose coupling: Components don’t need to know each other directly.
- Scalability: Easy to add/remove services without breaking the system.
- Real-time responsiveness: Ideal for financial transactions, IoT, gaming, and streaming.
- Flexibility: Systems adapt quickly to new requirements.

# Disadvantages

- Complex debugging: Harder to trace event flows compared to linear systems.
- Event storming risk: Too many events can overwhelm poorly designed systems.
- Consistency issues: Event-driven systems often favor eventual consistency over immediate synchronization.

# Inner Function

1. Event occurs → e.g., “Order Placed” in an e-commerce app.
2. Producer emits event → the order service publishes the event.
3. Event broker routes event → middleware like Apache Kafka or RabbitMQ distributes it.
4. Consumers react → payment service charges the card, inventory service updates stock, notification service emails the customer.

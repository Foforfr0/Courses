# UDP

Source: []()

Tags: **[]**

# Index

- [**What is?**](#whats-it)
- [**Main applications**](#main-applications)
- [**Advantages**](#advantages)
- [**Disadvantages**](#disadvantages)
- [**Inner function**](#inner-function)

# What´s it?

This protocol is not reliable. When some data is lost, the protocol dont care about it and still sending datagrams. Is faster than TCP.

# Main Applications

# Advantages

# Disadvantages

# Inner Function

```Mermaid
sequenceDiagram
  participant Client
  participant Server
  Client ->> Server: Data request
  Server ->> Client: Datagram
  Server ->> Client: Datagram
  Server ->> Client: Datagram
```

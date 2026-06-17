# TCP

Source: []()

Tags: **[]**

# Index

- [**What is?**](#whats-it)
- [**Main applications**](#main-applications)
- [**Advantages**](#advantages)
- [**Disadvantages**](#disadvantages)
- [**Inner function**](#inner-function)

# What´s it?

Is a reliable protocol, because per every package sended an Handshake is done. TCP detects lost package and resend it. TCP buffers and hands them in the rigth sequence.

# Main Applications

# Advantages

# Disadvantages

# Inner Function

```Mermaid
sequenceDiagram
    participant Client
    participant Server
    Client ->> Server: SYN
    Server ->> Client: SYN + ACK
    Client ->> Server: ACK
    Note over Client,Server: Connection established
    Client ->> Server: Data request
    Server ->> Client: Data response
    Client ->> Server: ACK
    Note over Client,Server: Data transfer phase
    Server ->> Client: ACK + FIN
    Client ->> Server: ACK + FIN
    Server ->> Client: FIN
    Note over Client,Server: Connection closed
```

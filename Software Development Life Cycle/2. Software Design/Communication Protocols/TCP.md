# TCP

Is a reliable protocol, because per every package sended an Handshake is done. TCP detects lost package and resend it. TCP buffers and hands them in the rigth sequence.

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

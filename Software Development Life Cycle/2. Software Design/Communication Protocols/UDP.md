# UDP

This protocol is not reliable. When some data is lost, the protocol dont care about it and still sending datagrams. Is faster than TCP.

```Mermaid
sequenceDiagram
  participant Client
  participant Server
  Client ->> Server: Data request
  Server ->> Client: Datagram
  Server ->> Client: Datagram
  Server ->> Client: Datagram
```

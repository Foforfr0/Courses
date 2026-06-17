# NAT - Network Address Translation

Source: []()

Tags: **[]**

# Index

- [**What is?**](#whats-it)
- [**Main applications**](#main-applications)
- [**Advantages**](#advantages)
- [**Disadvantages**](#disadvantages)
- [**Inner function**](#inner-function)

# What´s it?

Network Address Translation (NAT) is a technique used in computer networks that allows multiple devices within a private network (such as your home or office) to share a single public IP address to connect to the internet. It is essential in IPv4 because it helps prevent address exhaustion and adds a layer of security by hiding internal IP addresses.

# Main Applications

# Advantages

- IPv4 address savings: Allows millions of devices to connect without each needing a unique public IP address.
- Enhanced security: Hides private IP addresses, hindering direct attacks from the internet.
- Flexibility: Facilitates connecting multiple devices on home and business networks.

# Disadvantages

- Additional latency: The translation process may slightly slow down the connection.
- Problems with P2P applications or online games: Some require direct connections, and NAT can complicate them.
- Not necessary with IPv6: This protocol has a much larger address space, reducing the need for NAT.

# Inner Function

- Internal devices have private IP addresses (e.g., 192.168.1.10).
- When accessing the internet, the NAT router replaces this private IP address with the public IP address assigned by the internet service provider.
- The router maintains a translation table to determine which response corresponds to each internal device.
- When a response arrives from the external server, the router translates the public IP address back to the correct private IP address and delivers the data to the appropriate device.

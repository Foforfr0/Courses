# WebRTC

Source: [**YouTube**: _WebRTC Deep Dive: The Protocol That Powers Every Video Call_](https://www.youtube.com/watch?v=Kn_3uHaKz7Q)

Tags: **[Real-Time]** **[Faster]** **[Web]** **[Multimedia]** **[P2P]**

# Index

- [**What is?**](#what-is)
- [**Main applications**](#main-applications)
- [**Incovenients**](#inconvenients)
  - [**TURN**](#turn)
  - [**Own Signaling Server**](#own-signaling-server)
  - [**Mobile networks hate UDP**](#mobile-networks-hate-udp)
  - [**Scalability**](#solving-scalability)
    - [**Selectiong Forwarding Unit**](#selecting-forwarding-unit)
    - [**SIMULCAST**](#simulcast)
    - [**Multipoint Control Unit**](#multipoint-control-unit)
- [**Inner function**](#inner-function)
  - [**The problem with NAT**](#problem-with-nat)
  - [**The hardest part**](#the-hardest-part)
- [**Example using Javascript**](#example-using-javascript)

# What is?

Protocol about ultra-low latency; faster than **TCP Handshake** and browers pulls a JSON response. Used in virtual meetings, streaming of video or audio, multiplayer games.

- In 2011 Google created an open-source project named WebRTC.
- By around 2017, every mayor browser had it built in.

Is **P2P (Peer to Peer)** connetion, with no server in the middle.

# Main Applications

WebRTC is not just for video, is to **RTCDataChannel** and that is any binary data between peers in the same connection with the same low latency. Is a **Real-Time Pipe**.

- Video.
- Audio.
- Cursor XY position.
- Multiplayer games sync state.
- File transfer.

# Inconvenients

## TURN

TURN server cost real money; between 10% and 15% of connections cannot stablish a direct path because come paths are extra hostile P2P (strict corporate firewalls, office networks that blocks UDP connections, certain mobile carrier setups). And TURN relays full media bandwith both directions.

There ir a self-hosted option called **COTURN**.

## Own Signaling Server

WebRTC don't ship one. It need to be built, usually as a small WebSocket service.

## Mobile Networks Hate UDP

Mobile networks block certain ports, some carriers throttle it. So, TURN server need to suply UDP with TCP and over TLS as fallback trasport.

## Agent's relationship

Safari and Chrome do not always negotiate codecs the same way.

## Recording

Recording is a separate problem entirely because SFU's forward packets, they don't decode them.

## Scalability

Each peer in the connection need to have `Streams = (N x (N-1)) / 2`

- 2 Peers - 1 Connection.
- 3 Peers - 3 Connections.
- 4 Peers - 6 Connections.
- 5 Peers - 10 Connections.
- 6 Peers - 15 Connections.
- 10 Peers - 45 Connections.
- 100 Peers - 4,950 Connections.

WebRTC is not **scalable**.

### Selecting Forwarding Unit

To handle sessions with a large number of peers, **SFU (Selecting Forwarding Unit)** is used.

Now, Every peer is talking with a main server, not with another peer. The server only retransmit the data, no decode, no re-encode, don´t look the content.

### SIMULCAST

For example, on a Zoom meeting, each peer sends two or three quality versions of the video at the same time and the **SFU** select the rigth one for each viewer (Speacker view, Grid view, sidebar)

### Multipoint Control Unit

MCU is another architecture. MCU decode every incoming stream, mix all in one and send that final video to each peer. But decoding and re-encoding is expensive with the CPU use

# Inner Function

WebRTC don´t functions like HTTP transactions, with requests, responses, stateless and no memory.

```Mermaid
sequenceDiagram
  participant Client
  participant Server
  Client->>Server: GET /index.html
  Server->>Client: 200 OK
  Note left of Client: HTTP connection using using TCP
```

WebRTC is used in comunication P2P. Is a session, with only one negotiation and keeping it alive. Is persistent using UDP. There are no requests. The communication lifecycle is:

- Side #1 sends a description about what it cand do, like: what video formats it support, audio formats, how network looks like.
- Side #2 pick what it can match and sends it back its own description.
- Once both are agree, the call begins.
- The file with the description about de communication restrictions of the 2 sides is called **SDP - Session Description Protocol**.

```Mermaid
sequenceDiagram
  participant Peer A
  participant Peer B
  Peer A ->> Peer B: SDP offered
  Peer B ->> Peer A: SDP accepted
  Peer A -> Peer B: Connection opened
  Note left of Peer A: Using UDP
```

The innner process about WebRTC is the next. Implementing another sub protocols.

```Mermaid
---
title: WebRTC
---
kanban
  Negotiation - WHAT TO SEND
    [SDP
    Session Description Protocol]
    [What codecs and streams]
  Connectivity - HOW TO REACH
    [ICE
    Interactive Connectivity Establishment
    Pick the best path or network between peers]
    [STUN
    Discover your own public IP]
    [TURN
    Relay when direct fails
    Kicks in when direct connection between two peers is actually impossible]
  Security
    [DTLS
    TLS, but for UDP]
    [Key exchange]
  SRTP
    [SRTP
    Secure Real-Time Media Packets]
    [RTCP
    Control channel - stats & sync]

```

## Problem with NAT

To have a connetion between two IP's like `10.0.0.xx` and a IP like `192.168.1.xx` the two IP's need to be public. You communicate througt your modem, but the other side don´t wanna communicate with your modem but yes with your own device.

To solve the problem:

1. **Signaling server**: Both devices connect to a common server on the public internet. Usually over WebSockets. The server jobs is only to be a matchmaker. WebRTC don't specify how to signaling works.
2. **STUN discovers public IPs**: Each peer talks to a STUN server to know how each IP looks like from de other peer.
3. **ICE finds best path**: Each peer collects a list of every possible address it migth be reachable at. Each one is called an **ICE candidate**:
   1. Local network.
   2. Public address from STUN.
   3. Maybe a relay address from TURN.
4. **Direct P2P connection**: Each peer send all their **ICE candidates** through the **signaling server**. The fastest path is taked.

## The hardest part

The hardest part is the **Signaling server** building

# Example using Javascript

```Javascript
// Create a Peer connection
// Which STUN server to use
const pc = new RTCPeerConnection({
  iceServers: [{ urls: 'stun:stun.l.google.com:19302' }]
});

// Grab the user's camera and microphone, adding those tracks.
const stream = await navigator.mediaDevices.getUserMedia({
  video: true, audio: true
});
stream.getTracks().forEach(track =>
  pc.addTrack(track, stream)
);

// Create a SDP offer
const offer = await pc.createOffer();
await pc.setLocalDescription(offer);
signalingChannel.send({ type: 'offer', spd: offer });
// Set the remote description
// Both sides are agree
signalingChannel.on('answer', async ({ sdp }) => {
  await pc.setRemoteDescription(sdp);
});

// ICE keeps testing peers until something connects
pc.onicecandidate = ({ candidate }) => {
  if (candidate)
    signaling.send({ type: 'ice', candidate});
};

// Handler fire on the receiving side
pc.ontrack = ({ streams }) => {
  videoElement.srcObject = streams[0];
};
```

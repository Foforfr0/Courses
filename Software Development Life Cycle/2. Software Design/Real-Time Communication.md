# Real-Time Communication

## Polling

Requests for recurring requests, with dead spots in the timeline. Without proper header management, this can lead to problems. The headers to use are:

- ETag.
- If-Non-Match.

## WebSocket

A live, bidirectional, low-latency tunnel communication medium is opened.

## Server Send Events - SSE

It has only one communication direction, from the server to the client. If the connection drops, the server retries.

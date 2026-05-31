---
title: SignalR
tags:
  - signalr
  - real-time
  - communication
  - websockets
  - .net
---

# SignalR

## Overview

Great Nuget package for real-time communication, has easy implementation and structure. Quite hard to create a client on Delphi 2009, though.


It has a lot of cool transport protocols with automatic [[fallback]]

## Key Concepts

- **Hubs**: The main abstraction for real-time communication
- **Connections**: Persistent connections between clients and servers
- **Groups**: Logical grouping of connections
- **Transports**: Different ways to establish connections (WebSockets, Server-Sent Events, Long Polling)

## SignalR Protocols

SignalR supports multiple transport protocols with automatic fallback:

### 1. WebSockets (Primary)
- **Best performance**: Full-duplex communication
- **Lowest latency**: Real-time bidirectional communication
- **Automatic reconnection**: Built-in connection management
- **Browser support**: Modern browsers (IE10+, Chrome, Firefox, Safari)

### 2. Server-Sent Events (SSE)
- **Fallback option**: When WebSockets unavailable
- **Server-to-client only**: One-way communication from server
- **Automatic reconnection**: Built-in retry mechanism
- **Browser support**: IE10+, Chrome, Firefox, Safari

### 3. Long Polling
- **Final fallback**: When other transports fail
- **Universal compatibility**: Works in all browsers
- **Higher latency**: Polling-based communication
- **Resource intensive**: More server resources required

### 4. Forever Frame (Legacy)
- **IE8/IE9 support**: Only used for older Internet Explorer
- **Deprecated**: Not recommended for new applications
- **Limited functionality**: Basic communication only

## Transport Fallback Order

SignalR automatically tries transports in this order:

1. **WebSockets** (if supported)
2. **Server-Sent Events** (if WebSockets fail)
3. **Long Polling** (if SSE fails)
4. **Forever Frame** (IE8/IE9 only)

## Protocol Selection Logic

```csharp
// SignalR automatically negotiates the best available transport
var connection = new HubConnectionBuilder()
    .WithUrl("https://your-server/signalr")
    .Build();

// You can also force a specific transport
var connection = new HubConnectionBuilder()
    .WithUrl("https://your-server/signalr")
    .WithTransport(HttpTransportType.WebSockets) // Force WebSockets only
    .Build();
```

## Transport Detection

SignalR performs transport negotiation:
1. **Negotiation request**: Client requests available transports
2. **Server response**: Server provides supported transports
3. **Transport selection**: Client chooses best available transport
4. **Connection establishment**: Client connects using selected transport



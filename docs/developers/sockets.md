---
title: Develop with Sockets
sidebar_position: 3
slug: /developers/sockets
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# Developing with Fleetbase Sockets

Real-time functionality is critical for modern logistics operations. Fleetbase utilizes SocketCluster, an efficient WebSocket-based pub/sub messaging system, to enable real-time features like location tracking and status updates. This guide will help you understand how to connect to and use Fleetbase's WebSocket channels effectively.

## Setting Up SocketCluster Client

Before you can subscribe to any channel, you need to set up your SocketCluster client. Here’s how you can do it:

### 1. Installation

First, install the SocketCluster client if it's not already included in your project:

```bash
npm install socketcluster-client
```

### 2. Connecting to the Server

Use the following JavaScript code to connect to the SocketCluster server:

```javascript
import socketClusterClient from 'socketcluster-client';

// Setup to connect to the Fleetbase Socket (socket.fleetbase.io) on port 8000
const socketConfig = {
    hostname: 'socket.fleetbase.io',
    secure: true,
    port: 8000,
    path: '/socketcluster/',
    connectTimeout: 10000,
    disconnectOnUnload: true,
};

const socketClusterClient = socketClusterClient.create(socketConfig);

// Listen for successful connection
(async () => {
    for await (let event of socketClusterClient.listener('connect')) {
        console.log('Connected to the server!');
    }
})();

// Listen for connection error
(async () => {
    for await (let event of socketClusterClient.listener('error')) {
        console.error('Connection Error:', event);
    }
})();
```

### 3. Subscribing to Channels

Once your client is set up and connected, you can subscribe to specific resource channels based on the `{type}.{id}` format. Each resource, like a driver, has its own channel.

#### Example: Subscribing to a Driver's Channel

Here's how to subscribe to a driver’s channel for real-time updates:

```javascript
// Replace 'driver_iox3ekU' with the actual driver ID
const channelName = 'driver.driver_iox3ekU';
const channel = socket.subscribe(channelName);

// Listen to channel for events
await channel.listener('subscribe').once();

// Listen for channel subscription
(async () => {
    for await (let output of channel) {
        const { event, data } = output;

        // Handle the location change of driver
        if (event === 'driver.location_changed') {
            showDriverOnMap(data.location);
        }
    }
})();
```

## Handling Data

Data received through channels can include a variety of information depending on the resource type. For drivers, it might include location coordinates, status updates, and other telemetry data.

### Processing Incoming Data

```javascript
(async () => {
    for await (let output of channel) {
        const { event, data } = output;
        if (event === 'driver.location_changed') {
            console.log(`${event} - Driver location: Latitude ${data.location.coordinates[0]}, Longitude ${data.location.coordinates[1]}`);
        }
    }
})();
```

The incoming socket data will be structured like so:

```json
{
    "id": "<the event id>",
    "api_version": "<the api version>",
    "event": "<the event name>",
    "created_at": "<the event datetime>",
    "data": {
        "id": "<the resource id>",
        ...other attributes
        "additionalData": {}
    }
}
```

The data will typically be the updated properties of the resource subscribed to, the `event` will name the type of event. For example it could be `driver.location_changed` or `driver.updated`.

## Best Practices

-   **Error Handling:** Implement robust error handling, especially for connection issues and failed subscriptions.
-   **Security:** Ensure that all communications are secured using HTTPS and WSS, and that authentication tokens are managed securely.
-   **Resource Management:** Unsubscribe from channels when no longer needed to prevent memory leaks and unnecessary data traffic.

## Conclusion

By integrating Fleetbase's real-time capabilities using SocketCluster, you can significantly enhance the responsiveness and efficiency of your logistics operations. This guide provides the foundational knowledge needed to start developing with Fleetbase sockets, enabling dynamic, real-time interactions across your logistics platforms.

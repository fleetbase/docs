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

## Monitoring Socket Channels

Fleetbase provides a convenient and user-friendly interface for monitoring socket communications and real-time events within your application. This capability is accessible directly through the Developers extension in the Fleetbase console, making it easier to debug and track real-time data flows.

### Accessing the Socket Monitoring Interface

To start monitoring your socket channels, follow these steps:

1. **Navigate to the Developers Section:**
Log in to your Fleetbase console, and go to the Developers section by selecting it from the main menu.

2. **Open the Web Sockets Page:**
Click on "Web Sockets" within the Developers section. This action will direct you to a dedicated view that displays all the default channels associated with your organization and each API key.

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/view-socket-channels.png" style={{width: '800px'}} />
</div>

### Monitoring Default Channels

The Web Sockets page will automatically list the default channels used by your organization. This list provides an overview of active channels and helps you understand the typical data traffic and event patterns in your application.

### Listening to Custom Channels

If you need to monitor events on a specific resource channel or inspect custom traffic patterns, the interface allows for on-demand monitoring:

1. **Listen to a Custom Channel:**
Click the "Listen on custom channel" button. A prompt will appear asking you to enter the name of the channel you wish to monitor.

2. **Enter Channel Name:**
Type the channel name in the format `{type}.{id}` (e.g., `driver.driver_iox3ekU`) and confirm. This will subscribe you to the channel and start showing real-time events as they occur.

3. **View Event Data:**
Once subscribed, you will see a live feed of all events passing through the channel, along with their complete JSON data payloads. This feed is invaluable for debugging and verifying that your real-time data handling processes are functioning as expected.

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/view-custom-socket-channel.png" style={{width: '800px'}} />
</div>

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/listen-custom-socket-channel.png" style={{width: '800px'}} />
</div>

### Benefits of Using the Monitoring Tool

- **Real-Time Insights**: Gain immediate visibility into the data transmitted over your Web Sockets, which is crucial for real-time applications.
- **Debugging**: Quickly identify and resolve issues related to event handling or data formatting.
- **Verification**: Confirm that integrations and event-driven interactions are working correctly, ensuring that your application responds appropriately to real-time data.

By utilizing the built-in socket monitoring tools provided in the Fleetbase console, developers can effectively manage and troubleshoot real-time communications within their applications. This feature enhances your ability to maintain robust and responsive logistics services.

## Best Practices

-   **Error Handling:** Implement robust error handling, especially for connection issues and failed subscriptions.
-   **Security:** Ensure that all communications are secured using HTTPS and WSS, and that authentication tokens are managed securely.
-   **Resource Management:** Unsubscribe from channels when no longer needed to prevent memory leaks and unnecessary data traffic.

## Conclusion

By integrating Fleetbase's real-time capabilities using SocketCluster, you can significantly enhance the responsiveness and efficiency of your logistics operations. This guide provides the foundational knowledge needed to start developing with Fleetbase sockets, enabling dynamic, real-time interactions across your logistics platforms.

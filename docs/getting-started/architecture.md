---
title: Architecture
sidebar_position: 2
slug: /getting-started/architecture
---

## Overview

This document provides an overview of the Fleetbase platform's architecture. The Fleetbase architecture is designed to be robust, scalable, and efficient, supporting a variety of logistics operations through its modular components. The architecture diagram illustrates the interaction between the different components of the system.

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/fleetbase-architecture-diagram.svg" style={{width: '500px'}} />
</div>

### Fleetbase Console

The Console serves as the primary user interface for Fleetbase, providing a comprehensive dashboard for managing and monitoring all logistics operations. Built using Ember.js, a productive, battle-tested JavaScript framework, the Console is designed for building modern web applications with efficiency and scalability in mind.

#### Key Technologies:

- *Ember.js:* Utilizes Ember.js for building ambitious web applications. Ember's convention-over-configuration approach simplifies development and enhances productivity.
- *Ember Engines:* Implements ember-engines to achieve a modular architecture. This allows for better code organization, lazy loading of parts of the application, and improved performance by isolating specific functionalities in standalone, routable engines. 
- *Fleetbase Extensions*: Extensions in Fleetbase are implemented as ember-engines, enabling them to run in isolation from the core application. This isolation enhances stability and security by ensuring that extensions do not interfere with the core functionality. Despite running independently, these extensions use a centralized service and UI library, which allows them to integrate seamlessly with the main Fleetbase platform. This architecture supports scalability by enabling the addition of new features without impacting the existing system structure.
- *Fleetbase UI:* Fleetbase publishes a dedicated UI library `@fleetbase/ember-ui`, which houses a suite of pre-designed UI components. These components are tailored for building user interfaces that are consistent with the Fleetbase ecosystem. The UI library enables developers to maintain a uniform look and feel across different extensions and parts of the Fleetbase platform, ensuring that all user interfaces are not only functional but also visually integrated.

#### Key Functions:

- *Modular Architecture:* By leveraging ember-engines, the Fleetbase Console ensures that different aspects of the application can be developed, maintained, and scaled independently.

### Fleetbase API

The Fleetbase API serves as the backbone of the Fleetbase platform, handling all interactions between the various components and external integrations. Designed for high performance and reliability, the API facilitates robust data handling, real-time communications, and efficient task management.

#### Key Technologies:

- *Laravel:* The API is built using Laravel, a powerful and expressive PHP framework known for its elegant syntax and robust features. Laravel provides a comprehensive set of tools that make it ideal for building complex applications with demanding back-end requirements.
- *Octane on Caddy Server:* Utilizes Laravel Octane to supercharge the performance of the API by serving it on a Caddy server. This configuration enhances response times and concurrency, enabling the API to handle high loads efficiently.

#### Key Functions:

- *REST API:* Offers a comprehensive and consumable REST API that facilitates easy integration with other systems, supporting a wide range of operations from data management to real-time updates.
- *Webhooks:* Provides robust support for webhooks, allowing external systems to receive real-time notifications about events within the Fleetbase platform, enhancing integration capabilities.
- *Real-Time:* Manages real-time data flow throughout the platform, ensuring that all components are synchronized and updates are delivered promptly.
- *Task Management:* Effectively handles asynchronous tasks and queuing, leveraging advanced job processing capabilities to maintain high performance and responsiveness across operations.
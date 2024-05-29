---
title: Quick Start
sidebar_position: 2
slug: /quick-start
---

# Quick Start

Get started with the open-source version of Fleetbase in just 10 to 15 minutes. This guide is intended for developers with basic familiarity with command line tools and development processes.

## Prerequisites

Before you begin, ensure you have Docker installed on your machine. This guide uses Docker to simplify the setup of Fleetbase.

## Installation Steps

```bash
# 1. Clone the repository and enter directory:
git clone git@github.com:fleetbase/fleetbase.git && cd fleetbase

# 2. Start the services using docker-compose:
docker-compose up -d

# 3. Enter the application container (could also be `fleetbase_application_1`):
docker exec -ti fleetbase-application-1 bash

# 4. Run the Deploy script:
sh deploy.sh
```
## Configuration

There is various environment variables and services which are required to successfuly run Fleetbase depending on your setup. For a local/development setup the default environment variables should be enough, but if you're running it on AWS, DigitalOcean, Azure, or some other cloud provider you may need to tweak the configuration.

### Fleetbase API Configuration

- `CONSOLE_HOST`: Specifies the host and port for the Fleetbase console.
- `SOCKETCLUSTER_HOST`: Specifices the host of your SocketCluster service.
- `SOCKETCLUSTER_PORT`: Specifices the port of your SocketCluster service. (Defaults to 38000)
- `SOCKETCLUSTER_SECURE`: Specifices if the SocketCluster service should use WSS or WS to connect. (Defaults to `false`)
- `GOOGLE_MAPS_API_KEY`: Required if you plan to use Google API for geocoding. (More providers coming later)
- `IPINFO_API_KEY`: Used to retrieve user information based on their IP address.
- `TWILIO_SID`, `TWILIO_TOKEN`, `TWILIO_FROM`: Required for Fleetbase to send SMS via Twilio. (More providers coming later)

Setting up mail is also very important for Fleetbase to send critical emails and verifications. Since Fleetbase is built using Laravel you can use the <a href="https://laravel.com/docs/11.x/mail#configuration" target="laravel">Laravel mail guide</a> for setting up and configuring emails in Fleetbase.

### Fleetbase Console Configuration

Modify the environment settings in the `console/environments/*` directory:

- `API_HOST`: URL where the Fleetbase API is accessible. (Defaults to http://localhost:8000)
- `SOCKETCLUSTER_HOST`: Specifices the host of your SocketCluster service.
- `SOCKETCLUSTER_PORT`: Specifices the port of your SocketCluster service. (Defaults to 38000)
- `SOCKETCLUSTER_SECURE`: Specifices if the SocketCluster service should use WSS or WS to connect. (Defaults to `false`)

### OSRM Configuration

Additionally you may want to use your own OSRM routing engine, or OSRM compatible engine for routing and route optimization. You can easily do this by setting the `OSRM_HOST` environment variable on both the console and the API.

- `OSRM_HOST`: Specifies the host for the OSRM compatible routing engine to use. (Defaults to https://bundle.routing.fleetbase.io)

### Next Steps

After completing the installation and configuration, you should verify that all services are running correctly and the application is responsive. Explore the Fleetbase documentation for advanced configurations, features, and how to start adding custom extensions.

Congratulations on setting up Fleetbase! Start exploring the capabilities of your new logistics and supply chain operating system.

#### Cant find what you are looking for? [Raise a request](https://github.com/fleetbase/docs/issues) or join our [Community](https://discord.gg/HnTqQ6zAVn) ✌️ 
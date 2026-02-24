---
title: Quick Start
sidebar_position: 2
slug: /quick-start
---

# Quick Start

Get started with Fleetbase in just 5 minutes using our CLI-based installer. This guide is perfect for developers and operations teams who want to get up and running quickly.

## Prerequisites

Before you begin, ensure you have:

- **Node.js** 14+ and npm installed
- **Docker** and Docker Compose installed
- Basic familiarity with command line tools

## Installation

### Step 1: Install Fleetbase CLI

```bash
npm install -g @fleetbase/cli
```

### Step 2: Run the Installer

```bash
flb install-fleetbase
```

The interactive installer will prompt you for:
- **Host**: Use `localhost` for local development or `0.0.0.0` for external access
- **Environment**: Choose `development` or `production`
- **Directory**: Installation location (default: current directory)

### Step 3: Access the Console

Once installation completes (typically 3-5 minutes), open your browser to:

```
http://localhost:4200
```

Complete the onboarding wizard to create your admin account and set up your organization.

## What's Next?

### Configure External Services

For production use, configure these services in your environment:

- **Email**: SendGrid, Mailgun, Postmark, or SMTP
- **SMS**: Twilio for driver notifications
- **Maps**: Google Maps API for geocoding and routing
- **Monitoring**: Sentry for error tracking

See the [Configuration Guide](/getting-started/configuration) for detailed instructions.

### Register Developer Account (Optional)

If you plan to develop or publish extensions:

```bash
# Register your developer account
flb register --host localhost:8000

# Verify your email
flb verify -e your@email.com -c 123456 --host localhost:8000

# Set your registry token
flb set-auth flb_your_token --registry http://localhost:8000
```

Learn more in the [CLI Documentation](/extensions/cli).

### Explore Extensions

Install extensions to add functionality:

```bash
# Set up authentication first
flb set-auth your_token --registry http://localhost:8000

# Install an extension
flb install @fleetbase/storefront-api --path /path/to/fleetbase
```

Browse available extensions in the [Extension Marketplace](https://console.fleetbase.io/extensions).

## Alternative Installation Methods

If you prefer more control over the installation process:

- [**Manual Docker Installation**](/getting-started/install/with-docker) - For advanced users
- [**Install from Source**](/getting-started/install/from-source) - For developers and contributors

## Troubleshooting

### Ports Already in Use

If ports 8000, 4200, 3306, or 6379 are already in use, stop the conflicting services or modify the Docker Compose configuration.

### Docker Not Running

Ensure Docker is running before installation:

```bash
docker --version
docker compose version
```

### Need Help?

- [Troubleshooting Guide](/getting-started/troubleshoot)
- [Community Discord](https://discord.gg/fleetbase)
- [GitHub Issues](https://github.com/fleetbase/fleetbase/issues)

## Next Steps

After completing the quick start:

1. [**Architecture Overview**](/getting-started/architecture) - Understand how Fleetbase works
2. [**Configuration Guide**](/getting-started/configuration) - Configure external services
3. [**Fleet Ops Guide**](/guides/fleet-ops/introduction) - Start managing your fleet
4. [**API Documentation**](/developers/api) - Integrate with your applications

Congratulations on setting up Fleetbase! You're ready to start managing your logistics operations.

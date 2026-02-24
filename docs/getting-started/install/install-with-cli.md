---
title: Install with CLI (Recommended)
sidebar_position: 1
slug: /getting-started/install/with-cli
---

# Install with CLI

The fastest and easiest way to install Fleetbase is using the Fleetbase CLI tool. This method handles all the complexity of Docker setup, environment configuration, and service initialization automatically.

## Prerequisites

- **Node.js**: Version 14 or later
- **npm**: Comes with Node.js
- **Docker**: Docker Desktop (Mac/Windows) or Docker Engine (Linux)
- **Docker Compose**: Version 2.0 or later (usually included with Docker Desktop)

## Installation Steps

### 1. Install Fleetbase CLI

```bash
npm install -g @fleetbase/cli
```

Verify the installation:

```bash
flb --version
```

### 2. Run the Installation Command

```bash
flb install-fleetbase
```

The interactive installer will prompt you for:

- **Host**: The IP address or hostname to bind to (default: `localhost`)
  - Use `localhost` for local development
  - Use `0.0.0.0` to allow external access
  - Use your server's IP for production

- **Environment**: Choose between `development` or `production`
  - `development`: Optimized for local development with debug mode enabled
  - `production`: Optimized for production with caching and performance features

- **Directory**: Installation directory (default: current directory)
  - The CLI will create a `fleetbase` directory with all necessary files

### 3. Wait for Installation

The CLI will:
1. Create the installation directory
2. Generate Docker Compose configuration
3. Create environment files for API and Console
4. Pull Docker images
5. Start all services (MySQL, Redis, API, Console, SocketCluster)
6. Initialize the database

This process typically takes 3-5 minutes depending on your internet connection.

### 4. Access Fleetbase

Once installation is complete, you'll see:

```
🏁 Fleetbase is up!
   API     → http://localhost:8000
   Console → http://localhost:4200

ℹ️  Next steps:
   1. Open the Console URL in your browser
   2. Complete the onboarding process to create your admin account
```

Open your browser to `http://localhost:4200` to access the Fleetbase Console.

## Post-Installation

### Complete Onboarding

1. Open the Console at `http://localhost:4200`
2. Follow the onboarding wizard to:
   - Create your admin account
   - Set up your organization
   - Configure basic settings

### Configure External Services (Optional)

For production use, you'll want to configure:

- **Email Service**: For sending notifications and verifications
- **SMS Service**: For driver notifications (Twilio)
- **Maps Service**: For geocoding and routing (Google Maps)
- **Monitoring**: For error tracking (Sentry)

See the [Configuration Guide](/getting-started/configuration) for details.

### Register Developer Account (For Extension Development)

If you plan to develop or publish extensions:

```bash
# Register account
flb register --host localhost:8000

# Verify email
flb verify -e your@email.com -c 123456 --host localhost:8000

# Set auth token (provided after verification)
flb set-auth flb_your_token --registry http://localhost:8000
```

See the [CLI Documentation](/extensions/cli) for more details.

## Troubleshooting

### Port Already in Use

If you see errors about ports already in use:

```bash
# Check what's using the ports
lsof -i :8000  # API
lsof -i :4200  # Console
lsof -i :3306  # MySQL
lsof -i :6379  # Redis

# Stop the conflicting services or choose different ports
```

### Docker Not Running

Ensure Docker is running:

```bash
docker --version
docker compose version
```

If Docker isn't running, start Docker Desktop (Mac/Windows) or the Docker service (Linux):

```bash
# Linux
sudo systemctl start docker
```

### Permission Denied

On Linux, you may need to add your user to the docker group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

### Services Not Starting

Check the logs:

```bash
cd fleetbase  # or your installation directory
docker compose logs -f
```

## Updating Fleetbase

To update Fleetbase to the latest version:

```bash
cd fleetbase  # your installation directory
docker compose pull
docker compose up -d
```

See the [Upgrading Guide](/getting-started/upgrading) for more details.

## Uninstalling

To completely remove Fleetbase:

```bash
cd fleetbase  # your installation directory
docker compose down -v  # -v removes volumes (databases)
cd ..
rm -rf fleetbase
```

## Next Steps

- [Configuration Guide](/getting-started/configuration) - Configure external services
- [CLI Documentation](/extensions/cli) - Learn about CLI commands
- [Extension Development](/developers/building-an-extension) - Build your own extensions
- [API Documentation](/developers/api) - Integrate with the Fleetbase API

## Comparison with Other Installation Methods

| Method | Difficulty | Time | Best For |
|--------|-----------|------|----------|
| **CLI (Recommended)** | Easy | 5 min | Everyone, especially beginners |
| [Docker Manual](/getting-started/install/with-docker) | Medium | 15 min | Advanced users who want control |
| [From Source](/getting-started/install/from-source) | Hard | 30 min | Developers and contributors |

The CLI method is recommended for most users as it handles all the complexity automatically while still giving you full control over the installation.

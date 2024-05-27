---
title: Install with Docker
sidebar_position: 2
slug: /getting-started/install/with-docker
---

## Prerequisites

Before starting the installation, ensure the following prerequisites are met:

- **Docker:** Docker must be installed on the host machine. Ensure Docker Compose is also available for managing multi-container Docker applications.
- **Git:** Required for source code management.
- **Environment:** Prepare a production environment, preferably a virtual or dedicated server.
- **Domain Names:** Have domain names ready for the API and console, and ensure they are pointed to your server.
- **SSL Certificates:** For HTTPS, prepare SSL certificates. You can obtain these from a certificate authority or use Let’s Encrypt for free certificates.

## Installation Steps

### 1. Clone the Repository

Start by cloning the Fleetbase repository into a suitable directory on your server:
```bash
git clone https://github.com/fleetbase/fleetbase.git && cd fleetbase
```

### 2. Configure Docker Containers

Edit the docker-compose.yml file to suit production needs. This may involve configuring volumes for persistent data, adjusting environment variables, and ensuring proper network settings are applied.

### 3. Build and Start Services

Use Docker Compose to build and start the services. The -d flag runs them in detached mode:
```bash
docker-compose up --build -d
```

### 4. Access the Application Container

Enter the application container to perform initial setup tasks:
```bash
# On OSX or others the application name might be "fleetbase-application-1"
docker exec -ti fleetbase_application_1 bash
```

### 5. Run the Deployment Script

Execute the deployment script within the container to configure the database and other components:
```bash
sh deploy.sh
```

## Configuration

Configuration involves setting up environment variables and integrating external services.

### Fleetbase API Configuration

Configure environment variables in the Docker Compose file or a separate .env file for the API:

- `DATABASE_URL`: Full database connection string.
- `REDIS_URL`: Connection string for Redis.
- `MAIL_DRIVER`: Set up for production email sending.
- `TWILIO_SID`, `TWILIO_TOKEN`, `TWILIO_FROM`: For SMS functionality.

### Fleetbase Console Configuration

Modify the environment settings in the Docker setup for the console:

- `API_HOST`: Set this to the production API URL.
- `SOCKETCLUSTER_HOST`, `SOCKETCLUSTER_PORT`: Ensure these are configured for real-time functionalities.

### OSRM Configuration

If using a custom OSRM service, configure the `OSRM_HOST` environment variable for both the console and the API.

### Integrating SSL Certificates

Set up SSL certificates for secure communication:

- Use certbot or a similar tool to generate and configure SSL certificates for your domains.
- Configure HTTPS redirection and SSL termination in the Nginx or Caddy configuration within Docker.

### Scheduler and Worker Configuration
Ensure the scheduler and workers are correctly set up to handle background tasks:

```dockerfile
# Scheduler configuration for production
FROM scheduler as scheduler
ENTRYPOINT ["/sbin/ssm-parent", "-c", ".ssm-parent.yaml", "run", "--"]
CMD ["go-crond", "--verbose", "root:./crontab"]
```

## Verify and Monitor

After deployment, verify all components are functioning correctly:

- Access the Fleetbase console via its domain.
- Check connectivity between services.
- Monitor logs and performance to ensure stability.

## Backup and Maintenance

Set up regular backups for your databases and persistent volumes. Plan for regular maintenance updates and monitor Docker containers and services.

## Conclusion

This guide provides a comprehensive approach to deploying Fleetbase in a production environment using Docker. It covers the initial setup, configuration of essential components, and considerations for security and maintenance. Adjustments might be needed based on specific infrastructure or additional requirements.
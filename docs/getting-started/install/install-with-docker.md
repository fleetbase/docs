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

Edit or create a `docker-compose.override.yml` file to suit production needs. This may involve configuring volumes for persistent data, adjusting environment variables, and ensuring proper network settings are applied. You can also create a `api/.env` file to configure environment variables for Fleetbase. You can read more about configuration in the [configuration section](/getting-started/configuration) of this guide.

#### Example for Docker (docker-compose.override.yml):

This file can be used to overwrite default environment variables which are set in the `docker-compose.yml` file which ships with Fleetbase.

```yaml
services:
  application:
    environment:
      - DATABASE_URL=mysql://user:password@db:3306/fleetbase
      - REDIS_URL=redis://redis:6379
```

#### Example for Source (.env):

```plaintext
DATABASE_URL=mysql://user:password@localhost:3306/fleetbase
REDIS_URL=redis://localhost:6379
```

#### Environment Variables for Fleetbase

- `APP_URL`: The URL where your Fleetbase API is accessible. For development, you might use localhost.
- `CONSOLE_HOST`: The URL for the Fleetbase console. This is required for CORS configuration.
- `DATABASE_URL`: The full database connection URL, including username, password, host, and database name.
- `STOREFRONT_DB_CONNECTION`: The database connection name for the storefront.
- `QUEUE_CONNECTION`: Defines the queue backend, which in this case is Redis.
- `CACHE_DRIVER`: Specifies Redis as the caching backend.
- `CACHE_PATH`: The file path for storing cache files, useful if file-based caching is used.
- `CACHE_URL`: The URL for the Redis cache server.
- `REDIS_URL`: The URL for the Redis server used for caching and queues.
- `SESSION_DOMAIN`: The domain under which sessions are valid.
- `BROADCAST_DRIVER`: The technology used for broadcasting real-time messages.
- `MAIL_FROM_NAME`: Default name used in emails sent by the system.
- `APP_NAME`: The name of your application.
- `LOG_CHANNEL`: Specifies how application logging is handled.
- `MODEL_CACHE_ENABLED`: Toggle to enable/disable model caching.
- `RESPONSE_CACHE_ENABLED`: Toggle to enable/disable response caching.
- `RESPONSE_CACHE_DRIVER`: Specifies the caching driver for responses.
- `MAIL_MAILER`: Defines the mail backend, for example: Sendgrid, SES (Amazon Simple Email Service), Postmark.
- `IPINFO_API_KEY`: API key for IPInfo service, if used.
- `GOOGLE_MAPS_API_KEY`: API key for Google Maps services.
- `GOOGLE_MAPS_LOCALE`: Locale setting for Google Maps.
- `TWILIO_SID`: Account SID for Twilio, used for sending SMS.
- `TWILIO_TOKEN`: Authentication token for Twilio.
- `TWILIO_FROM`: The phone number from which Twilio SMS are sent.
- `SENTRY_LARAVEL_DSN`: The DSN for Sentry, used for error tracking.
- `OSRM_HOST`: The OSRM server to use, defaults to https://router.project-osrm.org

### 3. Build and Start Services

Use Docker Compose to build and start the services. The -d flag runs them in detached mode:
```bash
docker compose up --build -d
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

- `CONSOLE_HOST`: The URL for the Fleetbase console. Required for CORS configuration.
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
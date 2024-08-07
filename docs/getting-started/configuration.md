---
title: Configuration
sidebar_position: 4
slug: /getting-started/configuration
---

## Environment Variables

Environment variables are key-value pairs that influence the behavior of running processes in an application. For Fleetbase, they are crucial for setting up database connections, external service integrations, and operational parameters.

### How to Set Environment Variables

In Docker environments, environment variables are usually set in the `docker-compose.yml` file or a separate .env file that Docker Compose can reference. When setting up from source, you may configure them directly in a .env file in the application's root directory. If you're modifying or adding environment variables to a docker setup we suggest you use a `docker-compose.override.yml` so upgrades will be seamless.

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

### Env Variables for Fleetbase API

- `APP_URL`: The URL where your Fleetbase API is accessible. For development, you might use localhost.
- `CONSOLE_HOST`: The URL for the Fleetbase console.
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

### Env Variables for Fleetbase Console

The console has a different set of environment variables, mainly used to setup connection to API.

- `API_HOST`: The URL where your Fleetbase API is accessible. 
- `API_NAMESPACE`: The namespace which should be used for the Fleetbase API, typically will remain `int/v1`.
- `SOCKETCLUSTER_PATH`: The path to the socketcluster host instance. Defaults to `/socketcluster/`.
- `SOCKETCLUSTER_HOST`: The host of the socketclust instance. 
- `SOCKETCLUSTER_SECURE`: Whether or not to use a secure socketcluster connection. (`wss://` or `ws://`).
- `SOCKETCLUSTER_PORT`: The port of the socketclust instance. Defaults to `38000`.
- `OSRM_HOST`: The OSRM server to use, defaults to https://router.project-osrm.org

## Setting Up SSL

Secure Sockets Layer (SSL) is crucial for protecting your application's data in transit. Below are steps for setting up SSL in both Docker-based deployments and traditional server environments from source.

### SSL with Docker

1. Obtain an SSL Certificate:

Use Let’s Encrypt or a similar CA to obtain an SSL certificate. You can automate this with certbot:

```bash
sudo apt-get update
sudo apt-get install certbot
sudo certbot certonly --standalone -d yourdomain.com -d www.yourdomain.com
```

2. Configure Docker to Use SSL:

Modify your `docker-compose.override.yml` to mount the SSL certificates and configure your web server to use them:

```yaml
services:
  console:
    volumes:
      - /etc/letsencrypt/live/yourdomain.com/fullchain.pem:/etc/ssl/certs/fullchain.pem
      - /etc/letsencrypt/live/yourdomain.com/privkey.pem:/etc/ssl/private/privkey.pem
    environment:
      - SSL_CERTIFICATE=/etc/ssl/certs/fullchain.pem
      - SSL_CERTIFICATE_KEY=/etc/ssl/private/privkey.pem
```

3. Update Nginx Configuration:

Ensure your Nginx configuration within Docker utilizes these SSL certificates for HTTPS connections. You will need to modify the `console/nginx.conf` file and rebuild the console container.

### SSL from Source

1. Obtain SSL Certificates:
Similar to the Docker method, use certbot or manually place your SSL certificates in a secure directory on your server.

2. Configure Web Server:
Edit your web server’s configuration to use SSL:

For Nginx:

```nginx
server {
    listen       443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        root   /usr/share/nginx/html;
        try_files $uri $uri/ /index.html =404;
    }
}
```

3. Redirect HTTP to HTTPS:

Optionally, configure your server to redirect all HTTP traffic to HTTPS to ensure secure connections:

For Nginx:

```nginx
server {
    listen       443 ssl;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        root   /usr/share/nginx/html;
        try_files $uri $uri/ /index.html =404;
    }
}
```

## Conclusion
This configuration guide provides a comprehensive overview of setting up and securing Fleetbase in a production environment, covering essential environment variables and SSL configuration. Properly using this guide will help ensure that your Fleetbase installation is secure, reliable, and ready for production use.
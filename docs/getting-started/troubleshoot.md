---
title: Troubleshoot
sidebar_position: 6
slug: /getting-started/troubleshoot
---

Have an issue with the installation, try a few of these workarounds.

## Web Installer not working?

Fleetbase ships with a web based installer to simplify the setup process for non-technical users. This is installer may not always work, in this case if you are experiencing issues with the web based installer you should use this simple workaround to get going.

1. Login to the application container
```bash
docker exec -ti fleetbase_application_1 bash
```

2. Manually run the deploy script
```bash
sh deploy.sh
```

## CORS Errors

Cross-Origin Resource Sharing (CORS) errors can occur if your environment variables are not correctly set up to allow frontend access to your backend services. Follow these steps to ensure that your environment variables are configured properly in both the Fleetbase Console (frontend) and your backend.

### Frontend Environment Configuration

#### Set API_HOST in Fleetbase Console

Ensure that the `API_HOST` variable in your Fleetbase Console's environment configuration points correctly to your backend API. This setting is crucial for the frontend to make requests to the backend.

1. Locate the Environment Configuration File:
Navigate to the `console/environments/` directory and find the relevant .env.* file (e.g., .env.production for production environments).

2. Edit the Environment Variable:
Open the file and ensure that the `API_HOST` is set to the URL where your backend API is hosted. It should look something like this:
```plaintext
API_HOST=https://api.yourdomain.com
```

### Backend Environment Configuration

#### Step 1: Set CONSOLE_HOST in Backend

The `CONSOLE_HOST` environment variable should be correctly set in your backend configuration to allow CORS requests from the frontend.

1. Determine Configuration Method:
- If using Docker, locate your `docker-compose.override.yml` file or create one, (see configuration for more details)[/getting-started/configuration].
- If not using Docker, locate your backend's `api/.env` file.

2. Edit the Environment Variable:
- For Docker environments, add or modify the `CONSOLE_HOST` in your `docker-compose.override.yml`:
```yaml
services:
  application:
    environment:
      - CONSOLE_HOST=https://console.yourdomain.com
```
- For non-Docker environments, ensure your api/.env file includes:
```plaintext
CONSOLE_HOST=https://console.yourdomain.com
```

#### Step 2: Check CORS Configuration in Fleetbase

Ensure that your Fleetbase backend is configured to handle CORS correctly. This typically involves modifying the `api/config/cors.php` configuration file in the config directory:

- Open `api/config/cors.php` and review the paths, allowed_origins, allowed_methods, and allowed_headers to ensure they match your requirements.
- Make sure that the `allowed_origins` array includes the domain of your Fleetbase Console.

### Testing Changes

After making these changes, restart your services:

- If using Docker, run:
```bash
docker compose down && docker compose up -d
```
- For non-Docker setups, simply restart your server services.

Then, test by accessing the Fleetbase Console and verifying that CORS errors no longer occur when it interacts with the backend.


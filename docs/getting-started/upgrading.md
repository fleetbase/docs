---
title: Upgrading
sidebar_position: 5
slug: /getting-started/upgrading
---

## Overview

Upgrading Fleetbase involves pulling the latest changes from the Git repository, resolving any merge conflicts, and updating dependencies and configurations as needed. This guide provides instructions for both Docker and source installations.

## Upgrading Fleetbase with Docker

### Step 1: Pull Latest Changes
Navigate to your Fleetbase installation directory and use Git to fetch the latest updates:
```bash
cd /fleetbase
git pull origin main --no-rebase
```

### Step 2: Resolve Merge Conflicts

If you encounter merge conflicts after pulling the latest changes, you'll need to resolve these manually. Open the conflicting files and make the necessary changes to integrate new updates with your custom configurations.

### Step 3: Upgrade Container Images

After resolving any conflicts and committing the changes, you need to pull the latest Docker containers:
```bash
docker compose down && docker compose pull && docker compose up -d
```

This will stop the current containers, pull the container images with latest versions, and restart them in detached mode.

## Upgrading Fleetbase from Source

### Step 1: Pull Latest Changes

Just like with Docker, start by navigating to your Fleetbase directory and pulling the latest changes:

```bash
cd /fleetbase
git pull origin main --no-rebase
```

### Step 2: Resolve Merge Conflicts

Handle any merge conflicts that arise during the Git pull, ensuring that your modifications are preserved while incorporating the latest updates.

### Step 3: Update API Dependencies

Navigate to the API directory and run Composer to update PHP dependencies:
```bash
cd /fleetbase/api
composer install
```

This step ensures that all backend dependencies are up to date with the versions specified in composer.json.

### Step 4: Run Deployment Script

Execute the deployment script to apply database migrations and other necessary setup procedures:

```bash
sh deploy.sh
```

### Step 5: Rebuild the Console

Finally, move to the console directory and rebuild the frontend to apply any changes:

```bash
cd /fleetbase/console
pnpm install # Ensure all dependencies are up to date
pnpm build --environment production
```

## Post-Upgrade Verification

After completing the upgrade, verify that everything is running smoothly:

- Check the application logs for any errors or warnings.
- Test the functionality to ensure all components are working as expected.
- Verify that the API and console are accessible and responsive.

## Conclusion

This guide provides a structured approach to upgrading Fleetbase, whether you are using Docker or a traditional source installation. By following these steps, you can ensure that your Fleetbase deployment takes advantage of the latest features and improvements while maintaining stability and security.
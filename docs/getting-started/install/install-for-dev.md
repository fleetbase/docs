---
title: Install for Development
sidebar_position: 1
slug: /getting-started/install/for-development
---

### Prerequisites

- Docker and Docker Compose installed on your machine.
- Basic knowledge of terminal commands.

### Install Commands

```bash
# 1. Clone the repository and enter directory and get all submodules:
git clone git@github.com:fleetbase/fleetbase.git && cd fleetbase
git submodule update --init --recursive

# 2. Start the services using docker-compose:
docker-compose up -d

# 3. Enter the application container (could also be `fleetbase_application_1`):
docker exec -ti fleetbase-application-1 bash

# 4. Run the Deploy script:
sh deploy.sh

# 5. For development stop the console container
docker-compose down console

# 6. Run the development server for the console
cd console && pnpm install && pnpm start
```

### Linking Packages for Development

If you'd like to customize or contribute to a Fleetbase extension, the extension must be linked first for changes to appear. For working on frontend development you will need to link the packages via the `package.json`.

```json
// package.json
{
    "dependencies": {
        "@fleetbase/fleetops-engine": "link:../packages/fleetops"
    }
}
```

If you need to do backend development, packages must be linked as well via `composer.json`. See the example below for linking a local package to the Fleetbase API.

```json
// composer.json
{
    "repositories": [
        {
            "type": "path",
            "url": "../packages/core-api"
        },
        {
            "type": "path",
            "url": "../packages/fleetops"
        }
    ]
}
```

Fleetbase packages contain both the frontend and server side code. For a Fleetbase extensions the frontend code will be located in the `/addon` directory while the server side code will be located in the `/server` directory. You can learn more about developing your own extension or contributing to an existing extension in the developers section.
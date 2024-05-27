---
title: Troubleshoot
sidebar_position: 6
slug: /getting-started/troubleshoot
---

Have an issue with the installation, try a few of these workarounds.

## Installer not working?

If you encounter issues with the web based installer use this workaround to get going.

1. Login to the application container
```bash
docker exec -ti fleetbase_application_1 bash
```

2. Manually run the deploy script
```bash
sh deploy.sh
```
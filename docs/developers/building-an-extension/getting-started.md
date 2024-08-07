---
sidebar_position: 2
slug: /developers/building-an-extension/getting-started
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# Getting Started

This section guides you through the initial setup and scaffolding of a Fleetbase Extension using the Fleetbase CLI. For developers new to Fleetbase, we also offer an alternative method using the [starter extension repo](https://github.com/fleetbase/starter-extension).

## Installation of Fleetbase CLI

Begin by installing the Fleetbase CLI with npm:

```bash
npm i -g @fleetbase/cli
```

## Choosing the Scaffold Location

It's recommended to scaffold extensions directly within the `packages` directory of your Fleetbase installation. This approach ensures that your extensions are neatly organized and easily accessible within the Fleetbase ecosystem.


## Scaffolding a New Extension

Once the CLI is installed, you can scaffold a new extension by running:

```bash
flb scaffold --path packages/your-extension
```

You will be prompted to provide details about your extension, including:

- `-p, --path`: Specify the directory where the extension should be created.
- `-n, --name`: Name of your extension.
- `-d, --description`: A brief description of what your extension does.
- `-a, --author`: Name of the extension author.
- `-e, --email`: Contact email of the author.
- `-k, --keywords`: Relevant keywords for the extension.
- `-n, --namespace`: PHP Namespace for your extension.
- `-r, --repo`: Repository URL where the extension will be hosted.

## Using the Starter Extension Repository

Alternatively, if you prefer a more manual setup, clone the starter extension repository:

```bash
git clone git@github.com:fleetbase/starter-extension.git packages/your-extension
```

This method creates a basic template in your specified directory, ready for further development.

## Setting Up Fleetbase for Development

After scaffolding or cloning your extension, you need to set up your development environment. Follow the [setup instructions for Fleetbase development](/getting-started/install/for-development) to prepare your system.

## Linking Your Extension

### Link the Engine

Link your extension to the Fleetbase console by modifying `console/package.json`:

```json
// package.json
{
    "dependencies": {
        "@author/your-extension": "link:../packages/your-extension"
    }
}
```

Make sure to run `pnpm install` from the `./console` directory.

### Link the API

Ensure the API is aware of your new extension by updating `api/composer.json`:

```json
{
    "repositories": [
        {
            "type": "path",
            "url": "../packages/your-extension"
        }
    ]
}
```

Make sure to run `composer install` from the `./api` directory.

This setup integrates your extension into the Fleetbase environment, allowing for seamless development and testing.

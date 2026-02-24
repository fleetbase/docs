---
title: Fleetbase CLI
sidebar_position: 1
slug: /extensions/cli
---

## Introduction

FLB (Fleetbase CLI) is a command-line interface tool designed for managing Fleetbase Extensions and installations. It simplifies the process of installing Fleetbase, publishing and managing both npm and PHP Composer packages, and handling developer account registration for self-hosted instances.

The CLI is used to:
- Install Fleetbase with Docker
- Setup authentication with the Fleetbase Registry
- Register and manage developer accounts
- Install and uninstall extensions
- Scaffold new extensions for development
- Publish extensions to registries

### Installation

To install FLB, run the following command:

```bash
npm install -g @fleetbase/cli
```

## Quick Start: Installing Fleetbase

The easiest way to install Fleetbase is using the CLI:

```bash
npm install -g @fleetbase/cli
flb install-fleetbase
```

This command will guide you through an interactive installation process. See the [Installation Guide](/getting-started/install/with-cli) for detailed instructions.

## Quick Start: Developer Account Registration

For self-hosted instances, you'll need to register a Registry Developer Account to publish and install extensions:

### Step 1: Register Your Account

```bash
flb register --host localhost:8000
```

You'll be prompted for:
- **Username**: Your desired username
- **Email**: Your email address
- **Password**: A secure password (minimum 8 characters)
- **Name**: Your display name (optional)

### Step 2: Verify Your Email

Check your email for a verification code:

```bash
flb verify -e your@email.com -c 123456 --host localhost:8000
```

Upon successful verification, you'll receive your registry token.

### Step 3: Set Your Registry Token

```bash
flb set-auth flb_your_token_here --registry http://localhost:8000
```

### Step 4: You're Ready!

You can now publish and install extensions on your self-hosted instance.

## Usage

### Installing Fleetbase

Install Fleetbase using Docker with an interactive wizard.

```bash
flb install-fleetbase
```

**Options:**
- `--host <host>`: Host or IP address to bind to (default: `localhost`)
- `--environment <environment>`: `development` or `production` (default: `development`)
- `--directory <directory>`: Installation directory (default: current directory)

**Example:**
```bash
flb install-fleetbase --host 0.0.0.0 --environment production --directory /opt/fleetbase
```

This command will:
1. Create the installation directory
2. Generate Docker Compose configuration
3. Create environment files
4. Pull Docker images
5. Start all services
6. Initialize the database

See the [CLI Installation Guide](/getting-started/install/with-cli) for more details.

### Registry Developer Account Commands

#### Register a Developer Account

Create a new Registry Developer Account for self-hosted instances.

```bash
flb register
```

**Options:**
- `-u, --username <username>`: Username for your account
- `-e, --email <email>`: Email address
- `-p, --password <password>`: Password (minimum 8 characters)
- `-n, --name <name>`: Display name (optional)
- `-h, --host <host>`: API host (default: `https://api.fleetbase.io`)

**Example:**
```bash
flb register --host localhost:8000
flb register -u myusername -e my@email.com --host localhost:8000
```

#### Verify Email Address

Verify your email using the code sent to your inbox.

```bash
flb verify
```

**Options:**
- `-e, --email <email>`: Email address
- `-c, --code <code>`: Verification code from email
- `-h, --host <host>`: API host (default: `https://api.fleetbase.io`)

**Example:**
```bash
flb verify -e my@email.com -c 123456 --host localhost:8000
```

#### Resend Verification Code

Request a new verification code if the previous one expired.

```bash
flb resend-verification
```

**Options:**
- `-e, --email <email>`: Email address
- `-h, --host <host>`: API host (default: `https://api.fleetbase.io`)

**Example:**
```bash
flb resend-verification -e my@email.com --host localhost:8000
```

#### Generate or Regenerate Token

Generate a new registry token or regenerate an existing one.

```bash
flb generate-token
```

**Options:**
- `-e, --email <email>`: Email address
- `-p, --password <password>`: Password
- `-h, --host <host>`: API host (default: `https://api.fleetbase.io`)

**Example:**
```bash
flb generate-token -e my@email.com --host localhost:8000
```

**Note:** This command is useful for:
- Accounts created before automatic token generation
- Regenerating tokens for security reasons
- Lost or compromised tokens

**Important:** Each time you generate a token, it replaces the previous one.

### Setup Registry Auth Token

To install extensions, you must set up authorization linked to your Fleetbase account.

**For Self-Hosted Users:**
Use the developer account registration flow:
1. `flb register` - Create your account
2. `flb verify` - Verify your email
3. Token is automatically provided after verification

**For Cloud Users:**
Generate a registry token at [console.fleetbase.io/extensions/developers/credentials](https://console.fleetbase.io/extensions/developers/credentials)

**Set the token:**

```bash
flb set-auth [token]
```

**Options:**
- `-p, --path <path>`: Path of the Fleetbase instance (default: `.`)
- `-r, --registry <url>`: Registry URL (default: `https://registry.fleetbase.io`)

**Example:**
```bash
# For self-hosted
flb set-auth flb_your_token --registry http://localhost:8000

# For cloud
flb set-auth flb_your_token
```

### Login to Registry

Authenticate with the Fleetbase registry using your credentials.

```bash
flb login
```

**Options:**
- `-u, --username <username>`: Username for the registry
- `-p, --password <password>`: Password for the registry
- `-e, --email <email>`: Email associated with your account
- `-r, --registry <registry>`: Registry URL (default: `https://registry.fleetbase.io`)
- `--scope <scope>`: Scope for the registry (optional)
- `--quotes <quotes>`: Quotes option for `npm-cli-login` (optional)
- `--config-path <configPath>`: Path to the npm config file (optional)

**Example:**
```bash
flb login -u myusername -r http://localhost:8000
```

### Publishing a Extension

To publish an extension, navigate to the extension directory and run:

```bash
flb publish [path]
```

**Options:**
- `[path]`: (Optional) The path to the extension directory to be published. Defaults to the current directory.
- `-r, --registry <url>`: Custom registry URL

**Example:**
```bash
flb publish
flb publish ./my-extension
flb publish -r http://localhost:8000
```

For PHP only extensions, `flb` will automatically convert `composer.json` to `package.json` before publishing.

### Unpublishing a Extension

To unpublish an extension, use:

```bash
flb unpublish [extension]
```

**Options:**
- `[extension]`: (Optional) The name of the extension to unpublish. If not provided, FLB will attempt to determine the extension name from the current directory.
- `-r, --registry <url>`: Custom registry URL

**Example:**
```bash
flb unpublish
flb unpublish @fleetbase/my-extension
flb unpublish @fleetbase/my-extension -r http://localhost:8000
```

### Scaffolding a Extension

Fleetbase CLI has the ability to scaffold a starter extension if you intend to develop your own extension. This greatly speeds up the development process as it gives you a correct starting point to build on.

To scaffold an extension, use: 

```bash
flb scaffold
```

**Options:**
- `-p, --path <path>`: The path to place the scaffold extension
- `-n, --name <name>`: The name of the extension to scaffold
- `-d, --description <description>`: The description of the extension to scaffold
- `-a, --author <author>`: The name of the extension author
- `-e, --email <email>`: The email of the extension author
- `-k, --keywords <keywords>`: The keywords of the extension to scaffold
- `--namespace <namespace>`: The PHP Namespace of the extension to scaffold
- `-r, --repo <repo>`: The Repository URL of the extension to scaffold

**Example:**
```bash
flb scaffold -n my-extension -d "My custom extension" -a "John Doe"
```

### Installing a Extension

To install an extension, use: 

```bash
flb install [extension]
```

**Options:**
- `[extension]`: The name of the extension to install
- `-p, --path <path>`: (Optional) The path to the fleetbase instance directory. Defaults to the current directory

**Example:**
```bash
flb install @fleetbase/storefront-api
flb install @fleetbase/storefront-api --path /opt/fleetbase
```

### Uninstalling a Extension

To uninstall an extension, use: 

```bash
flb uninstall [extension]
```

**Options:**
- `[extension]`: The name of the extension to uninstall
- `-p, --path <path>`: (Optional) The path to the fleetbase instance directory. Defaults to the current directory

**Example:**
```bash
flb uninstall @fleetbase/storefront-api
flb uninstall @fleetbase/storefront-api --path /opt/fleetbase
```

### Bundling a Extension

To bundle an extension, use: 

```bash
flb bundle
```

or to bundle and upload the created bundle, use:

```bash
flb bundle --upload
```

**Options:**
- `-p, --path <path>`: Path of the Fleetbase extension (default: `.`)
- `--upload`: After bundling, upload the bundle to the Fleetbase registry using your authentication token
- `--auth-token <token>`: Auth token for uploading the bundle (used with `--upload` option)
- `-r, --registry <registry>`: Registry URL (default: `https://registry.fleetbase.io`)

**Example:**
```bash
flb bundle
flb bundle --upload --auth-token flb_your_token
```

### Uploading a Extension Bundle

To upload an extension bundle, use:

```bash
flb bundle-upload [bundleFile]
```

**Options:**
- `[bundleFile]`: Path to the bundle file to upload. If not provided, it will look for the bundle in the current directory
- `-p, --path <path>`: Path where the bundle is located (default: `.`)
- `--auth-token <token>`: Auth token for uploading the bundle. If not provided, the token will be read from the `.npmrc` file
- `-r, --registry <registry>`: Registry URL (default: `https://registry.fleetbase.io`)

**Example:**
```bash
flb bundle-upload
flb bundle-upload ./my-extension-1.0.0.flb.tgz
```

### Version Bump

To bump the version on an extension, use:

```bash
flb version-bump
```

**Options:**
- `-p, --path <path>`: Path of the Fleetbase extension (default: `.`)
- `--major`: Bump major version (e.g., `1.0.0` → `2.0.0`)
- `--minor`: Bump minor version (e.g., `1.0.0` → `1.1.0`)
- `--patch`: Bump patch version (e.g., `1.0.0` → `1.0.1`). This is the default if no flag is provided
- `--pre-release [identifier]`: Add a pre-release identifier (e.g., `1.0.0` → `1.0.0-beta`)

**Example:**
```bash
flb version-bump --patch
flb version-bump --minor
flb version-bump --major
flb version-bump --pre-release beta
```

## Self-Hosted vs Cloud

### Self-Hosted Users

If you're running Fleetbase on your own infrastructure:

1. Use `flb register` to create a Registry Developer Account
2. Verify your email with `flb verify`
3. Use the provided token with `flb set-auth`
4. Specify `--host` parameter for all commands pointing to your instance

**Example Workflow:**
```bash
# Install Fleetbase
flb install-fleetbase --host 0.0.0.0

# Register developer account
flb register --host localhost:8000

# Verify email
flb verify -e you@email.com -c 123456 --host localhost:8000

# Set auth token
flb set-auth flb_token_here --registry http://localhost:8000

# Install extensions
flb install @fleetbase/storefront-api --path /opt/fleetbase
```

### Cloud Users

If you're using Fleetbase Cloud (console.fleetbase.io):

1. Generate a registry token from the Console at [Extensions > Developers > Credentials](https://console.fleetbase.io/extensions/developers/credentials)
2. Use the token with `flb set-auth`
3. No need to specify `--host` parameter (defaults to cloud)

**Example Workflow:**
```bash
# Get token from console.fleetbase.io/extensions/developers/credentials

# Set auth token
flb set-auth flb_token_here

# Install extensions
flb install @fleetbase/storefront-api
```

## Configuration

FLB can be configured via command-line options. The most common options include:

- `-r, --registry [url]`: Specify a custom registry URL
- `-h, --host [url]`: Specify the API host for developer account operations
- `-p, --path [path]`: Specify the path to Fleetbase instance or extension

## Troubleshooting

### Authentication Issues

If you're having trouble authenticating:

1. Ensure you've verified your email address
2. Check that you're using the correct token
3. Verify the `--registry` URL matches your instance
4. Try regenerating your token with `flb generate-token`

### Installation Issues

If `flb install-fleetbase` fails:

1. Ensure Docker is running
2. Check that required ports are available (8000, 4200, 3306, 6379)
3. Verify you have sufficient disk space
4. Check Docker logs for errors

### Extension Installation Issues

If extension installation fails:

1. Ensure you've set up authentication with `flb set-auth`
2. Verify the extension name is correct
3. Check that the Fleetbase instance path is correct
4. Ensure the extension is compatible with your Fleetbase version

## Next Steps

- [Installation Guide](/getting-started/install/with-cli) - Install Fleetbase with CLI
- [Extension Development](/developers/building-an-extension) - Build your own extensions
- [Configuration Guide](/getting-started/configuration) - Configure Fleetbase
- [API Documentation](/developers/api) - Integrate with Fleetbase API

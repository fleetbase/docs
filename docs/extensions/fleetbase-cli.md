---
title: Fleetbase CLI
sidebar_position: 1
slug: /extensions/cli
---

## Introduction

FLB (Fleetbase CLI) is a command-line interface tool designed for managing Fleetbase Extensions. It simplifies the process of publishing and managing both npm and PHP Composer packages, particularly for Fleetbase extensions. 

The CLI is used to setup authentication with the Fleetbase Registry, install and uninstall extensions, as well as scaffold new extensions for development.

### Installation

To install FLB, run the following command:

```bash
npm install -g @fleetbase/cli
```

## Usage

### Publishing a Extension

To publish a extension, navigate to the extension directory and run:

```bash
flb publish [path]
```

- `[path]`: (Optional) The path to the extension directory to be published. Defaults to the current directory.

For PHP only extensions, `flb` will automatically convert `composer.json` to `package.json` before publishing.

### Unpublishing a Extension

To unpublish a extension, use:

```bash
flb unpublish [extension]
```

- `[extension]`: (Optional) The name of the extension to unpublish. If not provided, FLB will attempt to determine the extension name from the current directory.

### Setup Registry Auth Token

To install purchased extensions you must setup authorization first which is linked to your Fleetbase account. You can generate a registry token at [https://console.fleetbase.io/extensions/developers/credentials](https://console.fleetbase.io/extensions/developers/credentials)

To setup registry auth use:

```bash
flb set-auth [token] --path /fleetbase
```

- `-p, --path`: (Optional) The path to the fleetbase instance directory. Defaults to the current directory.

### Scaffolding a Extension

Fleetbase CLI has the ability to scaffold a starter extension if you intend to develop your own extension. This greatly speeds up the development process as it gives you a correct starting point to build on.

To scaffold a extension, use: 

```bash
flb scaffold
```

- `-p, --path`: The path to place the scaffold extension.
- `-n, --name`: The name of the extension to scaffold.
- `-d, --description`: The description of the extension to scaffold.
- `-a, --author`: The name of the extension author.
- `-e, --email`: The email of the extension author.
- `-k, --keywords`: The keywords of the extension to scaffold.
- `-n, --namespace`: The PHP Namespace of the extension to scaffold.
- `-r, --repo`: The Repository URL of the extension to scaffold.

### Installing a Extension

To install a extension, use: 

```bash
flb install [extension] --path /fleetbase
```

- `[extension]`: The name of the extension to install.
- `-p, --path`: (Optional) The path to the fleetbase instance directory. Defaults to the current directory.

### Uninstalling a Extension

To uninstall a extension, use: 

```bash
flb uninstall [extension] --path /fleetbase
```

- `[extension]`: The name of the extension to install.
- `-p, --path`: (Optional) The path to the fleetbase instance directory. Defaults to the current directory.

### Setting a Custom Registry

To specify a custom registry for publishing and unpublishing, use the `-r` or `--registry` option:

```bash
flb publish -r http://my-registry.com
flb unpublish -r http://my-registry.com
```

## Configuration

FLB can be configured via command-line options. The most common options include:

- `-r, --registry [url]`: Specify a custom registry URL.

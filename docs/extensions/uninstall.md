---
title: Uninstall Extensions
sidebar_position: 3
slug: /extensions/uninstall
---

## Introduction

Uninstalling an extension to Fleetbase is fairly simple using the Fleetbase CLI. This guide will walk you through uninstalling via the CLI, and uninstalling manually using `pnpm` and `composer`.

### Install Fleetbase CLI

Make sure you have installed the Fleetbase CLI globally.

```bash
npm install -g @fleetbase/cli
```

### Uninstall an Extension via CLI

You do not need a registry token to uninstall an extension, but it's advised to have the token registered regardless. Simply run the uninstall command using the Fleetbase CLI.

```bash
flb uninstall {extension}
```

### Uninstall an Extension Manually

Extensions can also be uninstalled manually using the default package manages `pnpm` and `composer`, but this is more tedious.

#### Uninstall Extension from Console

First uninstall the extension to the console by updating the `console/package.json` and removing the package, or simply using `pnpm`. Extensions are simply npm and composer packages combined. 

```bash
cd console
pnpm remove {extension}
```

#### Uninstall Extension from API

Next uninstall the API for the extension either by updating `api/composer.json` and removing the package, or simply using `composer`.

```bash
cd api
composer remove {extension}
```
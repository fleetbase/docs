---
title: Installing Extensions
sidebar_position: 2
slug: /extensions/install
toc_min_heading_level: 2
toc_max_heading_level: 5
---

## Introduction

Installing an extension to Fleetbase is fairly simple using the Fleetbase CLI. This guide will walk you through installing via the CLI, and installing manually using `pnpm` and `composer`.

### Install Fleetbase CLI

Get started by first installing the Fleetbase CLI globally.

```bash
npm install -g @fleetbase/cli
```

### Setup Auth with Registry

Fleetbase uses a registry for handling storage and authentication of extensions. In order to install purchesed extensions you will need to create an authentication token and configure it to your machine hosting Fleetbase.

#### Get a Registry Token

You can get a registry token from [Fleetbase](https://console.fleetbase.io/extensions/developers/credentials).

Generate a new registry token by clicking on the "Create new credentials" button in the top right area of the page. You will need to authenticate using your account password.

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/create-registry-token.png" style={{width: '100%'}} />
</div>

#### Set Registry Token

On your machine where you have Fleetbase installed navigate to the Fleetbase directory. Copy your registry token from the Extensions credentials page, and using the Fleetbase CLI use the `set-auth` command to register the token.

```bash
cd /fleetbase
flb set-auth "{token}"
```

This will register your registry token so that you can install extensions to your Fleetbase instance. If you're using a production or any deployed instance you should set your token as an environment variable `REGISTRY_TOKEN`. 

### Install an Extension via CLI

Now that you have you're registry token registered to your instance you can now install extensions. Simply run the install command using the Fleetbase CLI.

```bash
flb install {extension}
```

From the Extensions section of Fleetbase for "self managed" extensions, instructions will be provided on how as shown in the screenshot below. You can either use the extension namespace or id. Namespace will always be prefixed with `fleetbase/` then the actual reserved name of the extension.

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/extension-install-instructions.png" style={{width: '100%'}} />
</div>

### Install an Extension Manually

Extensions can also be installed manually using the default package manages `pnpm` and `composer`, but this is more tedious.

#### Install Extension to Console

First install the extension to the console by updating the `console/package.json` or simply using `pnpm`. Extensions are simply npm and composer packages combined. 

```bash
cd console
pnpm install {extension}
```
#### Install Extension to API

Next install the API for the extension either by updating `api/composer.json` or simply using `composer`.

```bash
cd api
composer require {extension}
```

That's all, instructions on this should be available to view on the extensions detail modal from within Fleetbase.



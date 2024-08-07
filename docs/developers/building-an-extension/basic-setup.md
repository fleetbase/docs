---
sidebar_position: 3
slug: /developers/building-an-extension/basic-setup
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# Basic Extension Setup

After scaffolding your extension, you need to configure both the Ember Engine and the Laravel package. This setup involves initializing the frontend code for the Ember Engine and setting up the API code for the Laravel package.

## Overview

- **Ember Engine**: Located in the `/addon` directory, this is where all your frontend code will reside.
- **Laravel Package**: Located in the `/server` directory, this is where you'll handle your API code.

## Setting Up the Ember Engine

Your Ember Engine configuration begins in `addon/engine.js`. This file includes the `setupExtension` function, which is called during initialization to configure your extension’s integration with Fleetbase.

Here is a basic example of `engine.js`:

```js
import Engine from '@ember/engine';
import loadInitializers from 'ember-load-initializers';
import Resolver from 'ember-resolver';
import config from './config/environment';
import services from '@fleetbase/ember-core/exports/services';

const { modulePrefix } = config;
const externalRoutes = ['console', 'extensions'];

export default class StarterEngine extends Engine {
    modulePrefix = modulePrefix;
    Resolver = Resolver;
    dependencies = {
        services,
        externalRoutes,
    };
    
    setupExtension = function (app, engine, universe) {
        // Register menu item in the header
        universe.registerHeaderMenuItem('Starter', 'console.starter', { icon: 'layer-group', priority: 5 });
    };
}

loadInitializers(StarterEngine, modulePrefix);
```

### Adding Dependencies

If your extension depends on other extensions, such as FleetOps, declare these dependencies in `engine.js`:

```js
export default class StarterEngine extends Engine {
    engineDependencies = ['@fleetbase/fleetops-engine'];
    
    setupExtension = function (app, engine, universe) {
        // Your setup code
    };
}
```

### `setupExtension` Arguments

The `setupExtension` function receives three arguments:
- **App Instance (`app`)**: Represents the entire Fleetbase Console application.
- **Engine Instance (`engine`)**: Represents the instance of your engine.
- **Universe Service (`universe`)**: Essential service for integrating with Fleetbase Console and other extensions.

### Registering in the Navbar

To add your extension to the Fleetbase Console navbar, use the `universe.registerHeaderMenuItem()` function. This function requires:
- **Name**: The display name for the navbar item.
- **Route**: The route for your extension, as defined in the `fleetbase.route` key of your `package.json`.
- **Options**: Optional parameters such as `icon` and `priority`.

Because routes are statically set at build time the route prefix can only be defined in the extensions `package.json` under the `fleetbase` key, which is shown in the example below. In the example the route prefix is set as `starter` this means your extension will sit at the `console.starter` route and the URL will be `/starter` in the Fleetbase Console.

You can change the `route` option to change the route and URL of your extension.

For example, your `package.json` might include:

```json
{
    "name": "@fleetbase/starter-engine",
    "version": "0.0.1",
    "description": "Starter Extension for Fleetbase",
    "fleetbase": {
        "route": "starter"
    }
}
```

This configuration means the route location of the extension will be `console.starter`. To register this with the navbar:

```js
universe.registerHeaderMenuItem('Starter', 'console.starter');
```

#### Custom Icons

You can use Font Awesome icons or custom icons for your extension. To use a Font Awesome icon:

```js
universe.registerHeaderMenuItem('Starter', 'console.starter', { icon: 'warehouse' });
```

For a custom icon, you will need to create a component using `ember-cli`:

Make sure you have `ember-cli` installed globally via `npm i -g ember-cli`. Now, from your extension directory run the ember generate command.

```bash
ember g component starter-brand-icon -gc
```

This will generate a new glimmer component at `addon/components/start-brand-icon` which will include a `js` file for the code and `hbs` (handlesbars) for the template. You can read more about Ember and Glimmer Components on the [official Ember.js docs](https://guides.emberjs.com/release/upgrading/current-edition/glimmer-components/).

Define the glimmer component with an `options` argument which contains a `width` and `height` property:

```js
import Component from '@glimmer/component';
import { tracked } from '@glimmer/tracking';

export default class StarterBrandIconComponent extends Component {
    @tracked width = 19;
    @tracked height = 19;
    
    constructor(owner, { options: { width = 19, height = 19 } }) {
        super(...arguments);
        this.width = width;
        this.height = height;
    }
}
```

Render the icon in the component’s template. For example, using SVG:

```hbs
<svg width={{this.width}} height={{this.height}} viewBox="0 0 352 322" xmlns="http://www.w3.org/2000/svg">
    <g fill-rule="nonzero" fill="none">
        <path />
    </g>
</svg>
```

You can also use a PNG, JPEG, or even a GIF like so using the Fleetbase `<Image />` component or the native HTML `<img />` element. In the example below we use the Fleetbase `<Image />` component.

```hbs
<Image src="https://url.com/images/start-brand-icon.png" width={{this.width}} height={{this.height}} />
```

Then, register the custom icon with `registerHeaderMenuItem`, first you must import the icon component to your `engine.js` then set it in the `iconComponent` option.

Additionally the `iconComponentOptions` property can take any other custom options you might require and they will be available to your component in the `options` argument.

```js
import StarterBrandIconComponent from './components/starter-brand-icon';

export default class StarterEngine extends Engine {
    setupExtension = function (app, engine, universe) {
        universe.registerHeaderMenuItem('Starter', 'console.starter', { 
            iconComponent: StarterBrandIconComponent, 
            iconComponentOptions: { width: 19, height: 19 }, 
            priority: 5 
        });
    }
}
```

#### `priority` Option

The `priority` option determines the position of your extension link in the navbar. 

- **Higher Priority Values**: Extension links with higher priority values are rendered later in the navbar. For example, if you set a priority of `10`, it will appear towards the end of the navbar, after items with lower priority values.
- **Lower Priority Values**: Extension links with lower priority values are rendered earlier in the navbar. For example, if you set a priority of `1`, it will appear before items with higher priority values.

By setting different priority values, you can control the order of your extension links relative to other items in the navbar. For example, a priority of `5` will place the link between items with priorities of `4` and `6`.


## Getting Started with Service Providers

The Service Provider in Laravel handles the setup of your extension's API. You can find this in `server/src/Providers/StarterServiceProvider.php`. If you used the CLI to scaffold your Service Provider name and backend namespace will be based on the name of your extension. 

In your service provider ensure that the `fleetbase/core-api` is registered:

```php
<?php

namespace Fleetbase\Starter\Providers;

use Fleetbase\Providers\CoreServiceProvider;

if (!Utils::classExists(CoreServiceProvider::class)) {
    throw new Exception('Starter cannot be loaded without `fleetbase/core-api` installed!');
}

class StarterServiceProvider extends CoreServiceProvider
{
    public function register()
    {
        $this->app->register(CoreServiceProvider::class);
    }

    public function boot()
    {
        // Register commands, observers, views, routes, and configs
        $this->mergeConfigFrom(__DIR__ . '/../../config/starter.php', 'starter');
    }
}
```

In future pages, we will cover:
- Setting up the API and using the Core API.
- Handling authentication and developing features.
- Creating integrations with other services.

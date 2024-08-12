---
sidebar_position: 4
slug: /developers/building-an-extension/developing-the-frontend
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# Developing the Frontend

Now that you have the basic setup for your extension, you will need to develop a frontend for the extension. If your extension is a sole integration this document will cover adding integrations to other extensions and the Fleetbase console. If your extension is a full module we will cover developing a UI for the module as well as cover the basics of controllers, routes, and components.


## Developing an Extension UI

If you are developing a full module extension you will need to build the overall UI which includes the sidebar for navigation, and the main and section view where the application UI and content will render to.

### Generate the Application Route

First start by generating an application route, this is the first route and template which will be loaded when users enter your extension. You can do this simply using `ember-cli` from your extension directory:

```bash
ember g application
```

This will generate two files:
- **`addon/routes/application.js`**: The initial route of your extension.
- **`addon/templates/application.hbs`**: The initial template of your extension, this will work as the "layout".

### Creating Application Layout

Inside your `application.hbs` we can create the initial UI using components from the `@fleetbase/ember-ui` package:

```hbs
<EmberWormhole @to="sidebar-menu-items">
    <Layout::Sidebar::Item @route="console.starter.home" @icon="home">Home</Layout::Sidebar::Item>
</EmberWormhole>

<Layout::Section::Container>
    {{outlet}}
</Layout::Section::Container>
```

- **`<EmberWormhole />`**: This is a component which can be used to register components or template inside of another element. Since the sidebar is persistent throughout Fleetbase and is rendered from the console application template you will need to "wormhole" your navigation items to the sidebar like the above example.
- **`<Layout::Section::Container />`**: This is a Fleetbase provided UI component which creates a standard container for rendering your extension UI.
- **`{{outlet}}`**: This is a standard Ember.js provided template helper which is used to render route and sub route content. For example when I click the "home link" it will render the template contents of the `console.starter.home` route into this `{{outlet}}`.

To add navigation panels which group menu items you can use the `<Layout::Sidebar::Panel />` component as shown below:

```hbs
<EmberWormhole @to="sidebar-menu-items">
    <Layout::Sidebar::Panel @open={{true}} @title="Research">
        <Layout::Sidebar::Item @route="console.starter.audits" @icon="magnifying-glass">Audits</Layout::Sidebar::Item>
        <Layout::Sidebar::Item @route="console.starter.reports" @icon="chart-line">Reports</Layout::Sidebar::Item>
    </Layout::Sidebar::Panel>
</EmberWormhole>
```

You can add as many panels or sidebar items as your extension needs, each route will be loaded and rendered into the `{{outlet}}`.

### Setup for Integrations in Layout

If your extension is integratable, meaning other extensions can integrate and add views and components then you will first need to create a registry via the `UniverseService` for other extensions to register to, then inside your UI you can render these integrations into your extension.

#### Creating Integration Registries

For example, let's say another extension enables additional functionality to our extension and we will allow additional menu items to our UI. Inside our `addon/engine.js` we will create a registry for this like so:

```js
export default class StarterEngine extends Engine {
    setupExtension = function (app, engine, universe) {
        // create all registries necessary
        universe.createRegistries([
            'engine:starter',
            'starter:component:map',
            'starter:component:global-search',
            'starter:component:audit-report-panel',
        ]);
    }
}
```

The above function `universe.createRegistries` will have created these registries for your extension which can be referenced in your application, in the example we created 4 registries each uniquely namespaced for their individual component or purpose.

#### Using Registries from Integrations

Now let's say we want to use the `engine:starter` registry a registry for menu items to our extension then in our template we can use the Fleetbase provided `<RegistryYield />` component.

The integrating extension would need to register a menu item from their own `engine.js` like so:

```js
import IntegrationControlPanel from './components/integration-control-panel';

export default class IntegratingEngine extends Engine {
    setupExtension = function (app, engine, universe) {
        // Register a menu item inside Starter extension
        universe.registerMenuItem('engine:starter', 'Integration Controls', {
            component: IntegrationControlPanel,
            registerComponentToEngine: '@org/starter-engine',
            icon: 'cog',
            slug: 'integration-controls',
        });
    }
}
```

The above `universe.registerMenuItem` function takes 3 arguments:

- **`registryName`**: This is the registry where the intended menu item will be registered to.
- **`title`**: This is the title of the menu item.
- **`options`**: These are options which add additional configuration for the menu item.
- **`options.component`**: This is the component which will be rendered when the menu item is clicked.
- **`options.registerComponentToEngine`**: This tells universe to register the above component into the engine being integrated to.
- **`options.icon`**: The icon for the menu item.
- **`options.slug`**: This is the url slug for the view rendered when the item is clicked.

#### Rendering from Registries

The above coverse integrating into another extension, but for this bit the focus is on handling integrations. So assuming all the above is done by another engine we can then render the menu items into our extension using the `RegistryYield` component mentioned previously. Here is how it would look:

```hbs
<EmberWormhole @to="sidebar-menu-items">
    <Layout::Sidebar::Item @route="console.starter.home" @icon="home">Home</Layout::Sidebar::Item>
    <RegistryYield @registry="engine:starter" as |menuItem|>
        <Layout::Sidebar::Item @route="console.starter.virtual" @icon={{menuItem.icon}}>{{menuItem.title}}</Layout::Sidebar::Item>
    </RegistryYield>
</EmberWormhole>
```

Alternatively you can also programatically get menu items from the `UniverseService` instance anywhere throughout your extension codebase. For example we can get the items as an array from a controller like so:

```js
import Controller from '@ember/controller';
import { inject as service } from '@ember/service';

export default class SomeController extends Controller {
    @service universe;
    
    get registryMenuItems() {
        return this.universe.getMenuItemsFromRegistry('engine:starter');
    }
}
```

## Adding Routes

Routes are URL's within your extension which allow a user to navigate to different parts of your extension. Routes can optionally have controllers which bind to the template allowing you to add functions or other properties. In Ember.js the route is responsible for loading data from the API if there is any, handling transitions, and controller setup if there is any.

Ember.js routes have very useful hooks which give you further control over each stage of the transition lifecycle. More about routes and it's API can be found on the [official Ember.js guide about routing](https://guides.emberjs.com/release/routing/).

To create a new route in your extension start by using the `ember-cli` generate command to generate a route like so:

```bash
ember g route home
```

### Nested Routes

Nested routing is also capable which takes advantage of the `{{outlet}}` template helper to render nested route templates from a parent route. Nested routes can also be generated like so:


```bash
ember g home/index
ember g home/analytics
ember g home/analytics/view
```

In the above example we have generated 3 nested routes, an `index` route which in Ember.js will always be loaded first and within the parent route, then another nested route `home/analytics` and a further nested route `home/analytics/view`. This is a great if your `home/analytics` route displays a list of items and you need to easily display specific details about an item within the same template via the `home/analytics/view` route. 

Additionally the URL mapping will be persistent so that when the user refreshed on the view route it will still load and render those specific analytic details.

### Define Routes

Routes will not work unless they are explicitly defined in your extensions `addon/routes.js` - you will need to manually define your newly generated routes here for them to work like so:

```js
import buildRoutes from 'ember-engines/routes';

export default buildRoutes(function () {
    this.route('home');
});
```

For defining nested routes it would look like this:

```js
import buildRoutes from 'ember-engines/routes';

export default buildRoutes(function () {
    this.route('home', function () {
        this.route('index');
        this.route('analytics', function () {
            this.route('view', { path: '/details/:id' });
        });
    });
});
```

The above example shows you can use the `path` option to customize the URL of the route and also dynamic parameters.

### Loading Data in Route

The Ember.js way is to load a routes data from the route itself, it provides a convenient hook called `model()` which the resolved data becomes available to both the template and controller as either `@model` from the template, or `this.model` in the controller.

Here is an example of loading data in a route using the Fleetbase provided `FetchService`:

```js
import Route from '@ember/routing/route';
import { inject as service } from '@ember/service';

export default class MyRoute extends Route {
    @service fetch;

    beforeModel(transition) {}

    model({ id }) {
        return this.fetch.get('analytics', { id });
    }

    afterModel(model) {}

    setupController(controller, model) {
        super.setupController(...arguments);
    }
}
```

In the above example we use the `FetchService` to load data from the api. The model first argument receives URL params or dynamic parameters which are set in the Router map. In this example included useful Route hooks as well which we will breakdown below, but further documentation on available hooks like `willTransition`, `didTransition`, `loading`, `activate` can be found on the [official Ember.js Route API documentation](https://api.emberjs.com/ember/release/classes/route/).

- **`beforeModel`**: Is a method which is called before the model is loaded, you can use this to do checks and then use the `Transition` [transition](https://api.emberjs.com/ember/release/classes/Transition) object to redirect or abort if needed.
- **`model`**: This method must return a promise which would then be resolved and passed to template and controller.
- **`afterModel`**: This method allows further functionality to be done from the route once the model is resolved.
- **`setupController`**: This method is called once the controller is initialized and mounted and the model has been resolved. Here you can do even more with the model and the controller if necessary.

## Using UI Components

Fleetbase extensions are built with `@fleetbase/ember-ui` package which is a Ember component library that provides convenient and common UI components, template helpers, modifiers and styling for your extension. We will cover the basic components which can be used to quiclky build a UI for your Fleetbase extension.

[Continue reading about provided user interface components](/developers/building-an-extension/user-interface-library)

## Using Core Services

Fleetbase extensions are built with the `@fleetbase/ember-core` package which provides critical core services and useful utilities to make developing extensions faster and easier. By now, you should already be familiar with the `UniverseService` which is used for integrations and functionality between the console and other extensions. 

Fleetbase extensions are able to easily inject these core services throughout the codebase (controllers, routes, components) using the Ember.js provided `inject` function like so:

```js
import Component from '@glimmer/component';
import { inject as service } from '@ember/service';

export default class MyComponent extends Component {
    @service fetch;
    @service universe;
}
```

Below are the core services Fleetbase exposes to extensions:

<table class="docs-table">
    <thead>
        <tr>
            <th style={{ width: '25%' }}>Service</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td valign="top">`AppCacheService`</td>
            <td valign="top">
                <p>This is a service which allows you to easily store and retrieve cache data from browser local storage.</p>
                <strong>Example Usage</strong><br />
                ```js
                this.appCache.set(key, value);
                const value = this.appCache.get(key);
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`ChatService`</td>
            <td valign="top">
                <p>This is a service which allows you to easily store and retrieve cache data from browser local storage.</p>
                <strong>Example Usage</strong><br />
                ```js
                @action async startChat() {
                    const chatChannel = await this.chat.createChatChannel('Chat Channel 1');
                    const chatParticipant = await this.chat.addParticipant(chatChannel, this.currentUser.user);
                    const message = await this.chat.sendMessage(chatChannel, chartParticipant, 'Hello Chat!');
                }
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`CrudService`</td>
            <td valign="top">
                <p>This is a convenient service for performing interactive CRUD operations on models. In the example below it will create a delete confirmation prompt.</p>
                <strong>Example Usage</strong><br />
                ```js
                @action deleteVehicle(vehicleModel) {
                    this.crud.delete(vehicleModel);
                }
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`CurrentUser`</td>
            <td valign="top">
                <p>This service allows you to load the currently authenticated user model and access properties for a current user. It can also be used to set user specifi local storage data. Since the current user is loaded already at the start of a session you should always be able to access the user from this service.</p>
                <strong>Example Usage</strong><br />
                ```js
                @action async getUser() {
                    try {
                        const user = await this.currentUser.promiseUser();
                        console.log(`${this.currentUser.name} loaded!`);
                        this.currentUser.setOption('some-preference', 1);
                    } catch (error) {
                        console.error(error.message);
                    }
                }

                @action async doSomethingWithUser() {
                    const preferences = await this.fetch.get(`user-preferences/${this.currentUser.id}`);
                }
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`FetchService`</td>
            <td valign="top">
                <p>This is a very convenient native fetch wrapper service which allows you to make authenticated request to the API, as well as handle uploads and downloads.</p>
                <strong>Example Usage</strong><br />
                ```js
                this.fetch.get();
                this.fetch.post();
                this.fetch.put();
                this.fetch.delete();
                this.fetch.download();
                this.fetch.uploadFile.perform(file);
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`NotificationsService`</td>
            <td valign="top">
                <p>This service is used to trigger notifications.</p>
                <strong>Example Usage</strong><br />
                ```js
                this.notifications.info();
                this.notifications.success();
                this.notifications.warning();
                this.notifications.error();
                try {
                    this.fetch.get();
                } catch (error) {
                    this.notifications.serverError(error);
                }
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`SocketService`</td>
            <td valign="top">
                <p>This service is a convenient wrapper around the socketcluster client which can be used to easily listen to socket channel events.</p>
                <strong>Example Usage</strong><br />
                ```js
                this.socket.listen(channelId, (event) => {
                    console.log('New incoming real-time event', event);
                });
                ```
            </td>
        </tr>
        <tr>
            <td valign="top">`UniverseService`</td>
            <td valign="top">
                <p>This service is crucial for developing extensions as it handles functionality and integration with the Fleetbase console and other extensions.</p>
                <strong>Example Usage</strong><br />
                ```js
                this.universe.registerUserMenuItem('Starter Analytics');
                ```
            </td>
        </tr>
    </tbody>
</table>

## Building an Integration Only

Some extensions do not require a full navigatable UI, instead an integration for example which adds a component to the Fleet-Ops order form will be much simpler. This is where the `UniverseService` becomes powerful in the ability to add UI functionality to other extensions, espescially core extensions.

In the following example will demonstrate adding an additional form box to the Fleet-Ops order form.

### The Engine.js

Let's say you want to create an extension for container haulage, typically these kinds of orders will require vessel and port details about the container. We can easily create a new form which renders into the Fleet Ops order form when a `type: haulage` order is being created. Let's call this extension "Haulage".

First generate the component which will be used to render a vessel details form into the Fleet Ops new order form.

```bash
ember g component vessel-details-form -gc
```

Next prepare your `addon/engine.js`:

```js
import Engine from '@ember/engine';
import loadInitializers from 'ember-load-initializers';
import Resolver from 'ember-resolver';
import config from './config/environment';
import services from '@fleetbase/ember-core/exports/services';
import VesselDetailsFormComponent from './components/vessel-details-form';

const { modulePrefix } = config;
const externalRoutes = ['console', 'extensions'];

export default class HaulageEngine extends Engine {
    modulePrefix = modulePrefix;
    Resolver = Resolver;
    dependencies = {
        services,
        externalRoutes,
    };
    engineDependencies = ['@fleetbase/fleetops-engine'];
    setupExtension = function (app, engine, universe) {
        // Register the Vessel Details form component
        universe.registerRenderableComponent('@fleetbase/fleetops-engine', 'fleet-ops:template:operations:orders:new', VesselDetailsFormComponent);
    };
}

loadInitializers(HaulageEngine, modulePrefix);
```

### The Component

Now head to your newly generated component and let's prepare it. Fleet-Ops order form registry will provide the `OrderModel` and the `Controller` for context which allows you to update the order and use the controller functions available.

```js
import Component from '@glimmer/component';
import { tracked } from '@glimmer/tracking';

export default class VesselDetailsFormComponent extends Component {
    @tracked order;
    constructor(owner, { order }) {
        super(...arguments);
        this.order = order;
    }

    @action updateVesselDetail(event, key) {
        this.order.setMeta(key, event.target.value);
    }
}
```

This component now has the Fleet Ops `OrderModel` instance as `this.order` available. We can store additional information about a Fleet Ops order using the `meta` property.

```hbs
{{#if (eq this.order.type "haulage")}}
    <ContentPanel
        @title="Vessel Details"
        @open={{true}}
        @pad={{true}}
        @panelBodyClass="bg-white dark:bg-gray-800"
    >
        <div class="grid grid-cols-3 gap-3">
            <InputGroup @name="Vessel Name" {{on "input" (fn this.updateVesselDetail "vessel-name")}}  />
            <InputGroup @name="Vessel IMO" {{on "input" (fn this.updateVesselDetail "vessel-imo")}}  />
            <InputGroup @name="Vessel ETA" {{on "input" (fn this.updateVesselDetail "vessel-eta")}}  />
        </div>
    </ContentPanel>
{{/if}}
```

Now when a haulage order type is selected the Fleet Ops order form will render you component allowing the user to key in vessel details to the order `meta` data which will be serialized and available as `get(order, 'vessel-name')` or on the backend as `$order->getMeta('vessel-name');`.






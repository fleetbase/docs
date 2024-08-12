---
sidebar_position: 6
slug: /developers/building-an-extension/backend-integration
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# Backend Integration

Integrating the frontend with the backend involves configuring adapters, models, and serializers on the frontend, and creating migrations, models, REST controllers, and routes on the backend. This guide will help you through each step to ensure a seamless integration.

The first few topics will cover configuring your engine for the backend integration.

## Engine Adapters

Your engine will need to setup adapters which determine how request for resources and the resource data is persisted. In EmberData, an Adapter determines how data is persisted to a backend data store. Things such as the backend host, URL format and headers used to talk to a REST API can all be configured in an adapter.

EmberData's default Adapter has some built-in assumptions about how a REST API should look. If your backend conventions differ from those assumptions, EmberData allows either slight adjustments or you can switch to a different adapter if your backend works noticeably differently.

Fleetbase provides a default `ApplicationAdapter` which all adapters should extend from.

In the following example let's say we're creating a inventory extension and we have a model `Warehouse`. In order for the engine to run CRUD operations and persist warehouse data we first need to create an adapter.

If all your resources exist on the same namespace in your backend, it is a good idea to create a base adapter for your model's adapters to extend from. Let's create the base adapter first:

```bash
ember g adapter inventory
```

After it's created it might look something like this:

```js
import ApplicationAdapter from '@fleetbase/ember-core/adapters/application';

export default class InventoryAdapter extends ApplicationAdapter {
    namespace = 'inventory/int/v1';
}
```

You'll notice this adapter only has one property which is `namespace`, this is because the provided `ApplicationAdapter` should cover everything else your backend will require, espescailly a Fleetbase integrated backend. The namespace determines the endpoint where all your REST routes will exist.

Now let's create the adapter for our `Warehouse` model.

```bash
ember g adapter warehouse
```

Now you can make your warehouse adapter just export your base `InventoryAdapter`:

```js
export { default } from './pallet';
```

This is because of ember's naming convention. Because the adapter name is `warehouse` this will create a generated URL based on the namespace for all Warehouse model request which look like `inventory/int/v1/warehouses`. You can read more about [customizing adapters on the official Ember docs](https://guides.emberjs.com/release/models/customizing-adapters/) if you need more customization.

## Engine Models

With your adapter created you can now create the model `WarehouseModel`, the model allows you to define attributes and methods your model will have:

```bash
ember g model warehouse
```

Your Warehouse model might look something like this:

```js
import Model, { attr, belongsTo, hasMany } from '@ember-data/model';

export default class WarehouseModel extends Model {
    @attr('string') name;
    @attr('string') address;
    @attr('string') city;
    @attr('string') state;
    @attr('string') zip;
    @attr('number') capacity;
    @attr('boolean') is_primary;
    @belongsTo('location') location;
    @hasMany('warehouse-section') sections;
    @hasMany('warehouse-dock') docks;
}
```

The Model has an example of attributes and relationships.

## Engine Serializers

If your model has relationships this needs to be defined inside your serializer.

```bash
ember g serializer warehouse
```

```js
import ApplicationSerializer from '@fleetbase/ember-core/serializers/application';
import { EmbeddedRecordsMixin } from '@ember-data/serializer/rest';

export default class WarehouseSerializer extends ApplicationSerializer.extend(EmbeddedRecordsMixin) {
    /**
     * Embedded relationship attributes
     *
     * @var {Object}
     */
    get attrs() {
        return {
            location: { embedded: 'always' },
            sections: { embedded: 'always' },
            docks: { embedded: 'always' },
        };
    }
}
```

## Migrations

On your backend you need to create the migrations which will create your database table schema which your model will use.

```bash
php artisan make:migration create_warehouses_table
```

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateWarehousesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('warehouses', function (Blueprint $table) {
            $table->increments('id');
            $table->uuid('uuid')->nullable()->unique();
            $table->foreignUuid('company_uuid')->nullable()->index()->references('uuid')->on('companies');
            $table->foreignUuid('location_uuid')->nullable()->index()->references('uuid')->on('locations');
            $table->string('name');
            $table->string('address');
            $table->string('city');
            $table->string('state');
            $table->string('zip');
            $table->integer('capacity');
            $table->boolean('is_primary')->default(false);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('warehouses');
    }
}
```


## Eloquent Model

Your backend will also need a model which corresponds to your new table created via the migration above:

```php
<?php

namespace Fleetbase\Inventory\Models;

use Fleetbase\Models\Model;
use Fleetbase\Traits\HasApiModelBehavior;
use Fleetbase\Traits\HasUuid;
use Fleetbase\Traits\Searchable;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Warehouse extends Model
{
    use HasUuid;
    use HasApiModelBehavior;
    use Searchable;

    /**
     * The database table used by the model.
     *
     * @var string
     */
    protected $table = 'warehouses';

    /**
     * The primary key for the model.
     *
     * @var string
     */
    protected $primaryKey = 'uuid';

    /**
     * The singularName overwrite.
     *
     * @var string
     */
    protected $singularName = 'warehouse';

    /**
     * These attributes that can be queried.
     *
     * @var array
     */
    protected $searchableColumns = ['name'];

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name',
        'address',
        'city',
        'state',
        'zip',
        'capacity',
        'is_primary',
    ];

    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = [
        'is_primary' => 'boolean'
    ];

    /**
     * @return BelongsTo
     */
    public function location()
    {
        return $this->belongsTo(Location::class);
    }

    /**
     * @return HasMany
     */
    public function sections()
    {
        return $this->belongsTo(WarehouseSection::class);
    }

    /**
     * @return HasMany
     */
    public function docks()
    {
        return $this->belongsTo(WarehouseDock::class);
    }
}
```

Notice the traits which are required to create an API resource `HasApiModelBehavior`, this is a trait which provides the models with the necessary methods to easily integrate with a REST controller providing CRUD operations.

## REST Controller

Your REST Controller will be required for CRUD operations on your model. Create your REST Controller `src/Http/Controllers/WarehouseController.php`:

```php
<?php

namespace Fleetbase\Inventory\Http\Controllers;

class WarehouseController extends PalletResourceController
{
    /**
     * The resource to query.
     *
     * @var string
     */
    public $resource = 'warehouse';
}
```

After this you can easily define the REST routes in `src/routes.php`:

```php
$router->fleetbaseRoutes('warehouse');
```

Using the `fleetbaseRoutes` method will automatically define the REST routes required which look like this:

```plaintext
GET /warehouses
GET /warehouses/:uuid
POST /warehouses
PUT /warehouses/:uuid
DELETE /warehouses/:uuid
```
## Making Requests

Now in your engine.js you can make request to your resource model easily using the Store Service which is provided by Ember.js, this utilizes your adapter, serializer, and model to build the request and also persist the data in the store.


```js
const warehouse = await this.store.findRecord('warehouse', id);
warehouse.set('name', 'Warehouse #1');
warehouse.save();
```

Or creating a warehouse model:

```js
const warehouse = this.store.createRecord('warehouse', {
    name: 'Warehouse #1'
});

warehouse.save().then((warehouse) => {
    console.log(`New Warehouse Created: ${warehouse.id}`);
});
```
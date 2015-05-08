
<a name="api_overview"></a>
## API Concepts

Schema provides a standardized interface to all APIs. This approach means you only have to learn the key concepts once to use them with each endpoint. A standardized approach promotes developer productivity and enables API tooling and other reusable components.

<a name="api_authentication"></a>
### Authentication

In general, API clients should handle the authentication protocol automatically and you don't need to know more than what is covered in <a href="#start_setup">Setup</a>.

Typically a client will authenticate when a connection is first established with the API, by passing `$client` and `$key` parameters along with the first request. The server will return the expected response if successful, or an error otherwise.

```
|          | ----- GET /<url> {$data: "...", $client: "x", $key: "y"} -----> |          |
|  CLIENT  |                                                                 |  SERVER  |
|          | <------------------ Response to GET /<url> -------------------- |          |
```

Another path can be taken if the client initiates a request without `$client` and `$key`, in which case the server will respond with a challenge token (nonce). The client will then perform a hash of the token and secret key, returning the result to the server, followed by the expected response to the first request if successful.

```
|          | ----------- GET /<url> {$data: "...", $client: "x"} ----------> |          |
|  CLIENT  | <-------------------- {$auth: "<nonce>"} ---------------------- |  SERVER  |
|          | ------------------AUTH {key: "<hashed-key>"} -----------------> |          |
|          | <------------------ Response to GET /<url> -------------------- |          |
```

This exchange allows for situations where a proxy may intermediate requests on an internal network without the need for SSL/TLS, and without exposing the client key along the way.

<a name="api_methods"></a>
### Methods

Schema implements common REST-API methods including `get`, `put`, `post`, and `delete`. Each method supports the same two arguments, `url` and `data`.

Note: `data` defaults to null.

Note: `url` has no specific length or depth limit.

#### GET

Use `get` to retrieve data. Results may be cached using the collection version returned by the server. See <a href="#api_caching">API Caching</a> for more information.

``` php
// Get a collection of records defined by <model>
$result = $client->get('/<model>');

// Get a single record defined by <model>, identified by <id>
$result = $client->get('/<model>/<id>');

// Get a <field> embedded within a single record defined by <model>, identified by <id>
$result = $client->get('/<model>/<id>/<field>');

// Get a <collection> of records linked from a record defined by <model>, identified by <id>
$result = $client->get('/<model>/<id>/<collection>');

// Example: Get a single stock record from within a product record
$result = $client->get('/products/<product-id>/stock/<stock-id>');
```
``` node
// Get a collection of records defined by <model>
client.get('/<model>', function(result) {...});

// Get a single record defined by <model>, identified by <id>
client.get('/<model>/<id>', function(result) {...});

// Get a <field> embedded within a single record defined by <model>, identified by <id>
client.get('/<model>/<id>/<field>', function(result) {...});

// Get a <collection> of records linked from a record defined by <model>, identified by <id>
client.get('/<model>/<id>/<collection>', function(result) {...});

// Example: Get a single stock record from within a product record
client.get('/products/<product-id>/stock/<stock-id>', function(result) {...});
```

The collection, record or field value will be returned. If not found, the server will return `null` with a status code `404`. This is different from the case where a URL identifying a record field is found and returns a value of `null`, while the status code returned is `200`.

See <a href="#api_querying">API Querying</a> for more information and use cases.


#### POST

Use `post` to create records in a collection or embedded array.

``` php
// Create a record in a collection defined by <model>
$result = $client->post('/<model>', array(...));

// Create a record in a <collection> linked from a record defined by <model>, identified by <id>
$result = $client->post('/<model>/<id>/<collection>', array(...));

// Create an entry in an <array-field> embedded within a record defined by <model>, identified by <id>
$result = $client->post('/<model>/<id>/<array-field>', array(...));

// Example: Create a product stock record
$result = $client->post('/products/<product-id>/stock', array(...));
```
``` node
// Create a record in a collection defined by <model>
client.post('/<model>', {...}, function(result) {...});

// Create a record in a <collection> linked from a record defined by <model>, identified by <id>
client.post('/<model>/<id>/<collection>', {...}, function(result) {...});

// Create an entry in an <array-field> embedded within a record defined by <model>, identified by <id>
client.post('/<model>/<id>/<array-field>', {...}, function(result) {...});

// Example: Create a product stock record
client.post('/products/<product-id>/stock', {...}, function(result) {...});
```

The newly created record will be returned. If not found, the server will return `null` with a status code `404`.

Note: When creating a record, you may pass fields that are not defined by the model. These values wll be stored in the record and can be queried like other fields, but are not type-checked or validated by the server in any way.


#### PUT

Use `put` to update records in a collection or fields in a record.

``` php
// Update a record in a collection defined by <model>, identified by <id>
$result = $client->put('/<model>/<id>', array(...));

// Update a <field> embedded within a record defined by <model>, identified by <id>
$result = $client->put('/<model>/<id>/<field>', array(...));

// Example: Update an order's billing information
$result = $client->put('/orders/<id>/billing', array(...));
```
``` node
// Update a record in a collection defined by <model>, identified by <id>
client.put('/<model>/<id>', {...}, function(result) {...});

// Update a <field> embedded within a record defined by <model>, identified by <id>
client.put('/<model>/<id>/<field>', {...}, function(result) {...});

// Example: Update an order's billing information
client.put('/orders/<id>/billing', {...}, function(result) {...});
```

The updated record or value will be returned. If not found, the server will return `null` with a status code `404`. Calling `put` against a collection will return `null` with a status code `400`.

When updating a record, you may pass fields that are not defined by the model. These values wll be stored in the record and can be queried like other fields, but are not type-checked or validated by the server in any way.

#### PUT: $unset

Use the $unset key in a `put` request to remove fields from a record.

``` php
// Remove the presence of one or more fields from a record
$result = $client->put('/<model>/<id>', array('$unset' => 'field1, field2, ...'));
```
``` node
// Remove the presence of one or more fields from a record
client.put('/<model>/<id>', {$unset: 'field1, field2, ...'}, function(result) {...});
```


#### DELETE

Use `delete` to remove records from a collection or values in a record.

``` php
// Remove a record from a collection defined by <model>, identified by <id>
$result = $client->delete('/<model>/<id>');

// Remove the value of a <field> embedded within a record defined by <model>, identified by <id>
$result = $client->delete('/<model>/<id>/<field>');

// Example: Delete a category
$result = $client->delete('/categories/<id>');
```
``` node
// Remove a record from a collection defined by <model>, identified by <id>
client.delete('/<model>/<id>', function(result) {...});

// Remove the value of a <field> embedded within a record defined by <model>, identified by <id>
client.delete('/<model>/<id>/<field>', function(result) {...});

// Example: Delete a category
client.delete('/categories/<id>', function(result) {...});
```

If a resource is identified by `url`, the deleted record or value will be returned. If not found, the server will return `null` with a status code `404`. Calling `delete` against a collection will return `null` with a status code `400`.

There is no explicit limit to the length or depth of a `url`.

<a name="api_querying"></a>
### Querying

There are several ways to specify the scope of a `get` request. 

#### Standard Query Parameters

Some words are reserved as standard query parameters:

- `where`: Set of field criteria to match records
- `sort`: Expression used to order record results
- `limit`: Maximum number of records to return, or `null` for unlimited
- `page`: Page number used in pagination
- `window`: Number of pages to consider in pagination
- `group`: Group query used in aggregation
- `search`: String to match records by search fields
- `expand`: Link fields to expand in a query result
- `include`: Additional `get` queries to include as fields in a result

If a record field name collides with that of a standard query parameter, you must pass the field inside a `where` parameter.

``` php
// Retrieve records with fields that collide with a standard parameter
$results = $client->get('/products', array(
    'where' => array('window' => 5)
));
```
``` node
// Retrieve records with fields that collide with a standard parameter
client.get('/products', {
    where: {window: 5}
}, function(results) {
    ...
});
```

Use of other standard query parameters are explained in sections that follow.

#### MongoDB Compatibility

Schema supports query operators that are compatible with MongoDB, including most operators documented in the <a href="http://docs.mongodb.org/manual/reference/operator/query/">MongoDB Manual (Query and Projection Operators)</a>.

``` php
// Query with Mongo compatible operators
$result = $client->get('/products', array(
    '$or' => array(
        array('size' => array('$gt' => 2, '$lt' => 10)),
        array('size' => null)
    )
));
```
``` node
// Query with Mongo compatible operators
client.get('/products', {
    $or: [
        {size: {$gt: 2, $lt: 10}},
        {size: null}
    ]
}, function(result) {
    ...
});
```

#### Query: Criteria

When specifying field criteria in a `get` request, *only* records matching *all criteria* will be returned.

``` php
// Find accounts where balance > 0 AND created in the last 24 hours
$results = $client->get('/accounts', array(
    'balance' => array('$gt' => 0),
    'date_created' => array('$gt' => time()-36400)
));
```
``` node
// Find accounts where balance > 0 AND created in the last 24 hours
client.get('/accounts', {
    balance: {$gt: 0},
    date_created: {$gt: Date.now()-36400}
}, function(results) {
    ...
});
```

#### Query: Search

Search a collection using the `get` method with a `search` string parameter.

``` php
// Find products that contain the string 'red'
$results = $client->get('/products', array('search' => 'red'));
```
``` node
// Find products that contain the string 'red'
client.get('/products', {search: 'red'}, function(results) {
    ...
});
```

Records will be returned where searchable fields (i.e. name) match the search string provided.

Search fields are configured in a `model`. See <a href="">Data Modeling</a> for more information.

Currently, search uses case-insensitive regular expression matching. This works for simple cases but is not ideal for product search, where fuzzy or phonetic matching is preferred. See the <a href="/roadmap">Product Roadmap</a> for a an estimate on our plan to support fuzzy and phonetic matching.

#### Query: Sort

Sort query results using the `sort` parameter.

``` php
// Get accounts sorted by last name in descending alphabetical order
$results = $client->get('/accounts', array('sort' => 'last_name desc'));
```
``` node
// Get accounts sorted by last name in descending alphabetical order
client.get('/accounts', {sort: 'last_name desc'}, function(results) {
    ...
});
```

The `sort` format consists of `<field> <direction>`, that is a field name, followed by a space, followed by a direction indicator. Direction is recognized as a full word (i.e. 'ascending' and 'descending') or as abbreviated (i.e. 'asc' and 'desc'). Direction is not case sensitive.

You can combine multiple sort fields in a string.

``` php
'last_name desc, first_name desc, balance asc'
```

Or array.

``` php
['last_name desc', 'first_name desc', 'balance asc']
```


#### Query: Expand

Expand links in query results using the `expand` parameter.

``` php
// Get a category with products expanded
$result = $client->get('/categories/tasty-treats', array('expand' => 'products'));
```
``` node
// Get a category with products expanded
client.get('/categories/tasty-treats', {expand: 'products'}, function(result) {
    ...
});
```

Result includes expanded field data.

``` json
{
    "id": "5407e0769fe97f9d4c712a5d",
    ...
    "products": {
        "count": 12,
        "results": [
            {
                "id": "5407e0929fe97f9d4c712a5e",
                ...
            },
            {
                "id": "5421d760a56462a55fd933a5",
                ...
            },
            ...
        ]
    }
}
```

You can expand up to 5 levels of nested links.

``` php
//  Get account with nested expand results
$result = $client->get('/accounts/5407e0769fe97f9d4c712a5d', array(
    'expand' => 'orders.invoices.credits'
));
```
``` node
//  Get account with deeply nested expand query
client.get('/accounts/5407e0769fe97f9d4c712a5d', {
    expand: 'orders.invoices.credits'
}, function(result) {
    ...
});
```

In the example above, the result will include `refunds` belonging to `credits` belonging to `invoices` belonging to `orders` belonging to the `account` with an ID of `5407e0769fe97f9d4c712a5d`.

``` json
{
    "id": "5407e0769fe97f9d4c712a5d",
    ...
    "orders": {
        "count": 3,
        "results": [
            {
                "id": "551f571a4f172353736a7a6d",
                ...
                "invoices": {
                    "count": 1,
                    "results": [
                        {
                            "id": "542b07655b2d60884689717c",
                            ...
                            "credits": {
                                "count": 1,
                                "results": [
                                    {
                                        "id": "5408dd2b3bdd10b335e6c349",
                                        ...
                                    }
                                ]
                            }
                        }
                    ]
                }
            },
            ...
        ]
    }
}
```

You can combine multiple expand fields in a string.

``` php
'products, parent, children'
```

Or array.

``` php
['products', 'parent', 'children']
```

#### Query: Include

Include multiple sources of data in single a query using the `include` parameter.

``` php
// Include product settings with a product result
$results = $client->get('/products/5407e0769fe97f9d4c712a5d', array(
    'include' => array(
        'settings' => array(
            'url' => '/settings/product'
        )
    )
));
```
``` node
// Include product settings with a product result
client.get('/products/5407e0769fe97f9d4c712a5d', {
    include: {
        settings: {
            url: '/settings/product'
        }
    }
}, function(results) {
    ...
});
```

A record query returns a field representing the `include` result.

``` json
{
    {
        "id": "5407e0769fe97f9d4c712a5d",
        ...
        "settings": {
            ...
        }
    }
}
```

A collection query returns the `include` result in each individual record. This allows you to include data related to reach record, and where a direct link does not exist in the model.

``` php
$results = $client->get('/accounts', array(
    'include' => array(
        'payments' => array(
            'url' => '/payments',
            'params' => array(
                'account_id' => 'id'
            )
        )
    )
));
```
``` node
client.get('/accounts', {
    include: {
        payments: {
            url: '/payments',
            params: {
                account_id: 'id'
            }
        }
    }
}, fuction(results) {
    ...
});
```

In the example above, the `include` query uses `params` to substitute the account ID. The result is a collection of account records, each with a `payments` field containing only payment records where `account_id` matches that of the individual account record.

``` json
{
    "count": 731,
    "results": [
        {
            "id": "54de8c4e66ea784f11ac3d53",
            ...
            "payments": {
                ...
            }
        },
        {
            "id": "5421d3b5a56462a55fd9339f",
            ...
            "payments": {
                ...
            }
        },
        ...
    ]
}
```


#### Query: Count

Retrieve a count of results matching a query using `:count`.

``` php
$count = $client->get('/products/:count', array(
    'active' => true
));
```
``` node
client.get('/products/:count', {
    active: true
}, function(count) {
    // 746
});
```

Result will be an `integer` value representing the number of matching records.


<a name="api_linking"></a>
### Linking

Link fields describe a relationship between a specific model and another resource.

``` json
{
    "name": "categories",
    "fields": {
        "id": {
            "type": "objectid"
        },
        ...
        "products": {
            "type": "link",
            "model": "products",
            "params": {
                "category_id": "id"
            }
        }
    },
    ...
}
```

Link fields encapsulate the parameters that define the relationship such as which `model` is referred to, a `key` field which contains a foreign key, `params` which describe additional field mappings, `data` which specify static value mappings, and/or a `url` string. 


Example: Link to a collection of records

``` json
{
    ...
    "fields": {
        "my_products": {
            "type": "link",
            "model": "products"
        }
    }
    ...
}
```

Example: Link to a record with foreign key

``` json
{
    ...
    "fields": {
        "my_product_id": {
            "type": "objectid"
        },
        "my_product": {
            "type": "link",
            "model": "products",
            "key": "my_product_id"
        }
    }
    ...
}
```

Example: Link to a collection with `data` values

``` json
{
    ...
    "fields": {
        "my_specific_product": {
            "type": "link",
            "model": "products",
            "data": {
                "sku": "T1000"
            }
        }
    }
    ...
}
```

Example: Link to a record with `url` and query params.

``` json
{
    ...
    "fields": {
        "pending_invoices": {
            "type": "link",
            "url": "/invoices?order_id={id}&status=pending"
        }
    }
    ...
}
```

Link fields represent a resource and will react to any request as if the resource were directly targeted.

``` php
// Retrieve a linked collection
$result = $client->get('/orders/{id}/pending_invoices', array(
    'id' => '542b07655b2d60884689717c'
));
```
``` node
// Retrieve a linked collection
client.get('/orders/{id}/pending_invoices', {
    id: 542b07655b2d60884689717c,
    ...
}, function(result) {
    ...
});
```



<a name="api_aggregation"></a>
### Aggregation

Group and aggregate data with `group` or `aggregation` parameters.

``` php
// Get order stats grouped by day, between a date range
$stats_by_day = $client->get('/orders', array(
    'group' => array(
        'count' => array('$sum' => 1),
        'sub_total' => array('$sum' => 'sub_total'),
        'avg_total' => array('$avg' => 'sub_total'),
        'day' => array('$dayOfMonth' => 'date_created'),
        'month' => array('$month' => 'date_created'),
        'year' => array('$year' => 'date_created')
    ),
    'where' => array(
        '$and' => array(
            array('date_created' => array('$gt' => '<start-date>')),
            array('date_created' => array('$lt' => '<end-date>'))
        )
    )
));
```
``` node
// Get order stats grouped by day, between a date range
client.get('/orders', {
    group: {
        count: {$sum: 1},
        sub_total: {$sum: 'sub_total'},
        avg_total: {$avg: 'sub_total'},
        day: {$dayOfMonth: 'date_created'},
        month: {$month: 'date_created'},
        year: {$year: 'date_created'}
    },
    where: {
        $and: {
            {date_created: {$gt: '<start-date>'}},
            {date_created: {$lt: '<end-date>'}}
        }
    }
}, function(statsByDay) {
    ...
});
```

Result will be a collection of data points grouped by day, with fields `count`, `sub_total`, and `avg_total`.

``` json
{
    "count": 30,
    "results": [
        {
            "count": 214,
            "sub_total": 7536.310,
            "avg_total": 35.216,
            "date": 30,
            "month": 5,
            "year": 2015
        },
        {
            "count": 178,
            "sub_total": 5870.850,
            "avg_total": 32.977,
            "date": 29,
            "month": 5,
            "year": 2015
        },
        ...

    ]
}
```

<a name="api_pagination"></a>
### Pagination

Paginate through a collection of records with `page` and `limit`.

``` php
// Retrieve page two of a collection, returning up to 25 results per page
$page_2_results = $client->get('/orders', array(
    'page' => 2,
    'limit' => 10
));
```

Result

``` json
{
    "count": 4383,
    "results": [

    ],
    "page": 2,
    "pages": {
        "1": {
            "start": 1,
            "end": 10
        },
        "2": {
            "start": 11,
            "end": 20
        },
        "3": {
            "start": 21,
            "end": 30
        },
        ...
    }
}
```

The most common use for pagination is to display page numbers and links on a web site. This can be implemented easily using the `pages` parameter of a collection result.

Example: Display pagination links (PHP)

``` php
$result = $client->get('/products', array('active' => true));

$current_page = $result['page']
$current_range = $result['pages'][$current_page];

echo 'Showing products '.$current_range['start'].' - '.$current_range['end'].' of '.$result['count'];

foreach ($result['pages'] as $page => $range) {
    echo '<a href="?page='.$page.'">Page '.$page.'</a>';
}
```

Another use case is to iterate through pages to process a large number of records.

``` php
$current_page = 1;
do {
    $orders = $client->get('/orders', array(
        'limit' => 50,
        'page' => $current_page
    ));
    foreach ($orders as $order) {
        ... process order ...
    }
    $current_page++;
}
while (isset($orders['pages'][$current_page]))
```
``` node
var async = require('async');

var orders = null;
var currentPage = 1;

async.doWhilst(
    function(doneWithPage) {
        client.get('/orders', {
            limit: 50,
            page: currentPage
        }, function(orderPage) {
            orders = orderPage;
            if (orderPage && orderPage.results.length) {
                async.each(orderPage.results,
                    function(order, doneWithOrder) {
                        ... process order ...
                        doneWithOrder();
                    },
                    function() {
                        doneWithPage();
                    }
                );
            } else {
                doneWithPage();
            }
        });
    },
    function() {
        currentPage++;
        return orders && orders.pages && orders.pages[currentPage];
    },
    function() {
        // Completed
        ...
    }
);
```

<a name="api_caching"></a>
### Caching

In all cases, API clients should handle cache management for you. Please refer to API client documentation for instructions on how to setup and maintain cache data. Protocol details follow.

Schema supports caching primarily at the collection level.

##### Collection Cache

The server stores a version number for each collection that is automatically incremented on update.

All requests to a particular collection can be cached without needing to check individual records. This approach has dramatic performance benefits on collections which do not frequently change, such as Products.

When a client connects to the API, it exchanges collection version info.

``` json
{$cached: {"com.products": 1471, "com.orders": 89412, "com.accounts": 5726, ...}
```

The server compares client versions with local versions and returns those that are changed.

``` json
{$cached: {"com.products": 1472, ...}
```

The client invalidates all cached records of a collection with new versions.

If a cached result contains expanded link data, it will be indexed by each collection which contributes to the result. For example, <i>a cached query that returns product and category data combined, <u>will be invalidated if either product or category collections are updated</u></i>.




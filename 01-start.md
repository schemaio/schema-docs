## Getting Started

Schema provides a full set of ecommerce models and functionality that can be quickly customized and adapted to any business requirement. Each feature has an API that can be called in any programming language from any server in the world.

The following documentation is intended for developers with at least a basic understanding of the web and database terminology.


<a name="start_setup"></a>
## Setup

Start by retrieving your API key from _Admin > Settings > Overview > API Keys_. If you don't have one yet, <a href="/new">create a free account now</a>.

Install an API client or framework. Schema offers several interfaces for popular programming languages:

- <a href="https://github.com/schemaio/schema-php-client">PHP API Client</a>
- <a href="https://github.com/schemaio/schema-php">PHP Template Framework</a>
- <a href="https://github.com/schemaio/schema-node-client">NodeJS API Client</a>
- <a href="https://github.com/schemaio/schema-python-client">Python API Client</a>
- <a href="https://github.com/schemaio/schema-ruby-client">Ruby API Client</a>
- <a href="https://github.com/schemaio/schema-java-client">Java API Client</a>

#### Connect the API Client


``` php
require('/path/to/schema-php-client/lib/Schema.php');

$client = new \Schema\Client('<client-id>', '<client-key>');
```
``` node
var Schema = require('schema-client')

var client = new Schema.Client('<client-id>', '<client-key>');
```
``` python
import schema

client = new schema.client({'id': '<client-id>', 'key': '<client-key>'});
```
``` ruby
require 'schema'

client = Schema::Client.new({'id': '<client-id>', 'key': '<client-key>'});
```



<a name="start_examples"></a>
## Examples

Common usage patterns for quick reference

``` php
// Get a record by ID
$record = $client->get('/products/{id}', array(
    'id' => '...'
));

// Get a collection of records with field criteria
$records = $client->get('/products', array(
    'active' => true
));

// Loop over a collection of records
foreach ($records as $record) {

    // Access a simple field
    $record['id']; // 553ee8a352279905269c60eb

    // Access a link field
    $record['categories']; // Array(...)
}

// Update a record by ID
$result = $client->put('/products/{id}', array(
    'id' => '...',
    'active' => true
));

// Create a new record
$result = $client->post('/products', array(
    'name' => 'New Product',
    'sku' => 'T1000',
    'active' => true
));

// Delete a record by ID
$result = $client->delete('/products/{id}', array(
    'id' => '...'
));
```
``` node
// Get a record by ID
client.get('/products/{id}', {
    id: '...'
}, function(record) {
    ...
});

// Get a collection of records with field criteria
client.get('/products', {
    active: true
}, function(records) {
    ...
});

// Loop over a collection of records
records.each(function(record) {

    // Access a simple field
    record.id; // 553ee8a352279905269c60eb

    // Access a link field
    record.categories(function(results) {
        ...
    });
});

// Update a record by ID
client.put('/products/{id}', {
    id: '...',
    active: true
}, function(result) {
    ...
});

// Create a new record
client.post('/products', {
    name: 'New Product',
    sku: 'T1000',
    active: true
}, function(result) {
    ...
});

client.delete('/products/{id}', {
    id: '...'
}, function(result) {
    ...
});
```

<div class="lang-block lang-node lang-javascript">
    <h4>JS Promises</h4>
    <p>
        Note: Each NodeJS client method also returns a <a href="">Promise</a>.
    </p>
</div>

``` node
client.get('/products')
.then(function(result) {
    ...
});
```


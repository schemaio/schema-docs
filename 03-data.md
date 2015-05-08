
<a name="data_overview"></a>
## Data Modeling

Schema supports a wide array of options for creating and customizing data models.

Models can be retrieved and updated using the API or Schema Admin interface.

You can use the API to explore standard models.

``` php
$product_model = $client->get('/:models/products');

print_r($product_model);
```
``` node
client.get('/:models/products', function(productModel) {
	console.log(productModel);
});
```

You can use the API to add fields or other properties to standard models.

``` php
$client->put('/:models/products', array(
	'fields' => array(
		'my_custom_field' => array(
			'type' => 'string',
			'default' => 'Hello World'
		)
	)
));

print_r($product_model);
```
``` node
client.put('/:models/products', {
	fields: {
		my_custom_field: {
			type: 'string',
			default: 'Hello World'
		}
	}
});
```

Any property of a model can be modified except `id` and `client_id`.


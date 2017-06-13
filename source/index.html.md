---
title: Hako API Reference

language_tabs:
  - shell: cURL

toc_footers:
  - <a href='https://dashboard.withhako.com'>Sign Up for a Developer Key!</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to **Hako**! Here you'll find comprehensive information for integrating with our API endpoints. If you have any questions, you can find help in our [Slack community](https://hakosupport.slack.com/) or you may email support@withhako.com.

The Hako API is organized around REST. All requests must include a `content-type` of `application/json` and the body must be a valid JSON.

JSON is returned by all API responses, including errors.

All **POST**, **PUT**, and **DELETE** requests require an **array** of one or more objects to create, update, or delete as input. Similarly, these calls output an **array** of affected objects upon success. This allows fewer calls to the Hako API when mass-updating inventory. In the future, the Hako API will allow individual object calls.

##Hosts Available
`https://api.withhako.com/v0/ (Production)`

# API Keys and Access

> To authorize, use this code:

```shell
curl "api_endpoint_here" -H 'Content-Type: application/json' 
  -u "your_API_key:"
```

To gain access to the Hako API, please create an account on our [Dashboard](https://dashboard.withhako.com) or log into your existing account. There you will find a Live API Key with which you can make requests. 

Hako expects your API key to be included in all API requests. Simply provide your API key as the basic auth username value using the `-u` flag. No password is needed:

`-u "your_API_key:"`

<aside class="notice">
Make sure to include the colon ":" after your API key when making cURL requests. Otherwise, cURL will ask for a password (to which you can press `enter` to skip the password).
</aside>

## Rate Limiting
The Hako API allows up to **120** requests per minute. If the max limit is reached, you will receive a `429` error and a message telling you to try again later.

# Items
## The Item Object
The **Item** object represents a product identified by a unique SKU (stock keeping unit). For example, a customer's store may sell t-shirts, in which long sleeves and short sleeves are two unique Item types.

###Attributes
Attribute | Type | Description
---------- | ------- | -------
name | String | The name of the Item
sku | String | The unique SKU for the Item
expires | Number (unix timestamp) | The unix date in which the product expires, used for perishable products
price | Number | The price of the product

##Create Items
```shell
curl -X POST -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "name": "Long Sleeve Tshirt", "expires": 1496203446, "price": 25}]' https://api.withhako.com/v0/items"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "name": "Long Sleeve Tshirt",
    "expires": 1496203446,
    "price": 25
  }
]
```

Creates **Item** objects with the specified SKUs and any other optional parameters provided. SKUs must be unique to all other existing Items.

Returns an **array** of created Item objects if successful.

### HTTP Request

`POST https://api.withhako.com/v0/items`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
sku | String | true 
name | String | false
expires | Number (unix timestamp) | false
price | Number | false

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
sku | The SKU of the created Item
name | The name of the created Item if specified on input
expires | The expiration date of the created Item if specified on input
price | The price of the created Item if specified on input
createdAt | The creation date of the created Item
updatedAt | The most recent updated date of the created Item

##Retrieve Items
```shell
curl -X GET -H 'Content-Type: application/json' "https://api.withhako.com/v0/items"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "name": "Long Sleeve Tshirt",
    "expires": 1496203446,
    "price": 25
  }
]
```

Retrieves the details of existing Items based on the search parameters given in the request.

Returns an **array** of retrieved Item objects if successful.

### HTTP Request

`GET https://api.withhako.com/v0/items`

### Query Parameters
_This endpoint requires a JSON object as input. The parameters for the object are as follows:_

Parameter | Type | Required
--------- | ------- | -------
sku | String | false 
name | String | false
expires | Number (unix timestamp) | false
createdAt | Number (unix timestamp) | false
updatedAt | Number (unix timestamp) | false
price | Number | false

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
sku | The SKU of the retrieved Item
name | The name of the retrieved Item if exists
expires | The expiration date of the retrieved Item if exists
price | The price of the retrieved Item if exists
createdAt | The creation date of the retrieved Item
updatedAt | The most recent updated date of the retrieved Item

##Update Items
```shell
curl -X PUT -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "updates": {"name": "Men\"s Long Sleeve Tshirt"}}]' "https://api.withhako.com/v0/items"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "name": "Men\'s Long Sleeve Tshirt",
    "expires": 1496203446,
    "price": 25
  }
]
```

Updates Item objects by setting the values of the parameters passed. Any parameters not provided will be left unchanged. 

Returns an **array** of updated Item objects if successful.

### HTTP Request

`PUT https://api.withhako.com/v0/items`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
sku | String | true
updates | JSON dictionary |  true

### Update Parameter Options
Parameter | Type
--------- | ------- | -------
sku | String
name | String
expires | Number (unix timestamp)
price | Number


### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
sku | The SKU of the updated Item
name | The name of the updated Item if exists
expires | The expiration date of the updated Item if exists
price | The price of the updated Item if exists
createdAt | The creation date of the updated Item
updatedAt | The most recent updated date of the updated Item

##Delete Items
```shell
curl -X DELETE -H 'Content-Type: application/json' --data '[{"sku": "hk-001"}]' "https://api.withhako.com/v0/items"
  -u "your_API_key:"
```

> Example Response:

```json
["hk-001"]
```

Permanently deletes Item objects. This cannot be undone.

Returns an **array** of deleted Item SKUs if successful.

### HTTP Request

`DELETE https://api.withhako.com/v0/items`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
sku | String | true

### Response Parameters
_This endpoint returns an **array** of one or more strings upon success. The parameters for the returned strings are as follows:_

Parameter | Type
--------- | -------
sku | The SKU of the deleted Item

# Warehouses
## The Warehouse Object
The **Warehouse** object represents locations where **Items** reside. For example, a customer's ecommerce store may sell t-shirts and have two Warehouses where product is kept.

###Attributes
Attribute | Type | Description
---------- | ------- | -------
name | String | The name of the Warehouse
warehouseID | String | A unique string identifying the Warehouse

##Create Warehouses
```shell
curl -X POST -H 'Content-Type: application/json' --data '[{"warehouseID": "warehouse-010", "name": "San Francisco Warehouse"}]' "https://api.withhako.com/v0/warehouses"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "warehouseID": "warehouse-010",
    "name": "San Francisco Warehouse"
  }
]
```

Creates **Warehouse** objects with the specified warehouseID and any other optional parameters provided. warehouseID must be unique to all other existing Warehouses.

Returns an **array** of created Warehouse objects if successful.

### HTTP Request

`POST https://api.withhako.com/v0/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
warehouseID | String | true 
name | String | false

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the created Warehouse
name | The name of the created Warehouse
createdAt | The creation date of the created Warehouse
updatedAt | The most recent updated date of the created Warehouse

##Retrieve Warehouses
```shell
curl -X GET -H 'Content-Type: application/json' "https://api.withhako.com/v0/warehouses"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "warehouseID": "warehouse-010",
    "name": "San Francisco Warehouse"
  }
]
```

Retrieves the details of existing Warehouses based on the search parameters given in the request.

Returns an **array** of retrieved Warehouses objects if successful.

### HTTP Request

`GET https://api.withhako.com/v0/warehouses`

### Query Parameters
_This endpoint requires a JSON object as input. The parameters for the object are as follows:_

Parameter | Type | Required
--------- | ------- | -------
warehouseID | String | false 
name | String | false
createdAt | Number (unix timestamp) | false
updatedAt | Number (unix timestamp) | false

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the retrieved Warehouse
name | The name of the retrieved Warehouse
createdAt | The creation date of the retrieved Warehouse
updatedAt | The most recent updated date of the retrieved Warehouse

##Update Warehouses
```shell
curl -X PUT -H 'Content-Type: application/json' --data '[{"warehouseID": "warehouse-010", "updates": {"name": "Bayview Warehouse"}}]' "https://api.withhako.com/v0/warehouses"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "warehouseID": "warehouse-010",
    "name": "Bayview Warehouse"
  }
]
```

Updates Warehouse objects by setting the values of the parameters passed. Any parameters not provided will be left unchanged. 

Returns an **array** of updated Warehouse objects if successful.

### HTTP Request

`PUT https://api.withhako.com/v0/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
warehouseID | String | true
updates | JSON dictionary |  true

### Update Parameter Options
Parameter | Type
--------- | ------- | -------
warehouseID | String
name | String

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the updated Warehouse
name | The name of the updated Warehouse
createdAt | The creation date of the updated Warehouse
updatedAt | The most recent updated date of the updated Warehouse

##Delete Warehouses
```shell
curl -X DELETE -H 'Content-Type: application/json' --data '[{"warehouseID": "warehouse-010"}]' "https://api.withhako.com/v0/warehouses"
  -u "your_API_key:"
```

> Example Response:

```json
["warehouse-010"]
```

Permanently deletes Warehouse objects. This cannot be undone.

Returns an **array** of deleted Warehouse warehouseIDs if successful.

### HTTP Request

`DELETE https://api.withhako.com/v0/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
warehouseID | String | true

### Response Parameters
_This endpoint returns an array of one or more strings upon success. The parameters for the returned strings are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the deleted Warehouse

# Inventory
## The Inventory Object
The **Inventory** object represents the count of an **Item** that exists at a specific **Warehouse**. For example, a customer's ecommerce store may sell t-shirts and have two Warehouses where product is kept. One Warehouse has a quantity of 10 t-shirt Items and the other has 20 t-shirt Items.

###Attributes
Attribute | Type | Description
---------- | ------- | -------
sku | String | The SKU of the Item
warehouseID | String | The warehouseID location of the Item
quantity | Number | The quantity of Items at specified Warehouse

##Create Inventory
```shell
curl -X POST -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "warehouseID": "warehouse-010", "quantity": 15}]' "https://api.withhako.com/v0/inventory"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "warehouseID": "warehouse-010",
    "quantity": 15
  }
]
```

Creates **Inventory** objects with the specified SKU, warehouseID, and quantity. If the specified SKU and warehouseID already has inventory saved in Hako, this endpoint will return an error and suggest you update the inventory count for the specified SKU and warehouseID instead.

Returns an **array** of created Inventory objects if successful.

### HTTP Request

`POST https://api.withhako.com/v0/inventory`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
warehouseID | String | true 
sku | String | true
quantity | Number | true

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the Warehouse that has Inventory
sku | The SKU of the Item that has Inventory
quantity | The quantity of Item in the Warehouse

##Retrieve Inventory
```shell
curl -H 'Content-Type: application/json' "https://api.withhako.com/v0/inventory"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "warehouseID": "warehouse-010",
    "quantity": 15
  }
]
```

Retrieves the inventory count of the requested SKU and warehouseID. In the future, this endpoint will allow retrieval of inventory counts across all warehouses based on SKU or warehouseID.

Returns an **array** of retrieved Inventory objects if successful.

### HTTP Request

`GET https://api.withhako.com/v0/inventory`

### Query Parameters
_This endpoint requires a JSON object as input. The parameters for the object are as follows:_

Parameter | Type | Required
--------- | ------- | -------
warehouseID | String | false 
sku | String | false
quantity | Number | false
createdAt | Number (unix timestamp) | false
updatedAt | Number (unix timestamp) | false

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the retrieved Inventory object
sku | The SKU of the retrieved Inventory object
quantity | The quantity of Items in the Warehouse
createdAt | The creation date of the retrieved Inventory object
updatedAt | The most recent updated date of the retrieved Inventory object

##Update Inventory
```shell
curl -X PUT -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "warehouseID": "warehouse-010", "updates": {"quantity": 14}}]' "https://api.withhako.com/v0/inventory"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "warehouseID": "warehouse-010",
    "quantity": 14
  }
]
```

Updates Inventory objects by setting the values of the parameters passed. Any parameters not provided will be left unchanged. 

Returns an **array** of updated Inventory objects if successful.

### HTTP Request

`PUT https://api.withhako.com/v0/inventory`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
sku | String | true
warehouseID | String | true
updates | JSON dictionary |  true

### Update Parameter Options
Parameter | Type
--------- | -------
warehouseID | String
sku | String
quantity | Number

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
warehouseID | The warehouseID of the updated Inventory Object
sku | The SKU of the updated Inventory object
quantity | The quantity of Items in Warehouse
createdAt | The creation date of the updated Warehouse
updatedAt | The most recent updated date of the updated Warehouse

##Delete Inventory
```shell
curl -X DELETE -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "warehouseID": "warehouse-010"}]' "https://api.withhako.com/v0/inventory"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "sku": "hk-001",
    "warehouseID": "warehouse-010"
  }
]
```

Permanently deletes Inventory objects. This is the same as assigning a quantity of **0** Items to a Warehouse. This cannot be undone.

Returns an **array** of deleted Inventory dictionaries containing SKUs and warehouseIDs if successful.

### HTTP Request

`DELETE https://api.withhako.com/v0/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

Parameter | Type | Required
--------- | ------- | -------
sku | String | true
warehouseID | String | true

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

Parameter | Type
--------- | -------
sku | The SKU of the deleted Inventory object
warehouseID | The warehouseID of the deleted Inventory object


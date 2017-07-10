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

The Hako API is organized around REST. All requests must include a `content-type` header of `application/json` and the body must be a valid JSON.

<aside class="notice">
If using Postman, be sure to send POST and PATCH request data as "raw". Wrap all strings (including dictionary keys and values) with double quotes.
</aside>

JSON is returned by all API responses, including errors.

All **POST**, **PATCH**, and **DELETE** requests require an **array** of one or more objects to create, update, or delete as input. Similarly, these calls output an **array** of affected objects upon success. This allows fewer calls to the Hako API when mass-updating inventory. In the future, the Hako API may allow individual object calls.

##Hosts Available
`https://api.withhako.com/v1/ (Production)`

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
Make sure to include the colon ":" after your API key when making cURL requests. Otherwise, cURL will ask for a password (to which you can press enter to skip the password).
</aside>

## Rate Limiting
The Hako API allows up to **120** requests per minute. If the max limit is reached, you will receive a `429` error and a message telling you to try again later.

# Items
## The Item Object
The **Item** object represents a product identified by a unique SKU (stock keeping unit). For example, a customer's store may sell t-shirts, in which long sleeves and short sleeves are two unique Item types.

###Attributes
| Attribute   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| name        | String                  | The name of the Item                     |
| sku         | String                  | The unique SKU for the Item              |
| expires     | Number (unix timestamp) | The unix date in which the product expires, used for perishable products |
| price       | Number                  | The price of the product                 |
| Inventories | Object                  | Contains associated inventory per Warehouse, if any |

##Create Items
```shell
curl -X POST -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "name": "Long Sleeve Tshirt", "expires": 1496203446, "price": 25, "inventory": [{"warehouseID": "w-SF", "quantity": 105}]}]' https://api.withhako.com/v1/items"
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
    "price": 25,
    "Inventories": [{
      "warehouseID": "w-SF",
      "quantity": 105
    }]
  }
]
```

Creates **Item** objects with the specified SKUs and any other optional parameters provided. SKUs must be unique to all other existing Items.

Returns an **array** of created Item objects if successful.

### HTTP Request

`POST https://api.withhako.com/v1/items`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter | Type                    | Required |
| --------- | ----------------------- | -------- |
| sku       | String                  | true     |
| name      | String                  | false    |
| expires   | Number (unix timestamp) | false    |
| price     | Number                  | false    |
| inventory | Array                   | false    |

### Inventory Parameters Options

<aside class="notice">
Important! A warehouse with the specified warehouseID must exist before you can create Items with inventory or you will receive an error as the response. You should create your warehouses before creating Items with inventory. You may initialize Items without inventory, though, and update the Item with inventory after creating the warehouse.
</aside>

_The Inventory parameter takes an array of objects, which shoud be defined as follows:_

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| warehouseID | String | true     |
| quantity    | Number | true     |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| sku         | String                  | The SKU of the created Item              |
| name        | String                  | The name of the created Item if specified on input |
| expires     | Number (unix timestamp) | The expiration date of the created Item if specified on input |
| price       | Number                  | The price of the created Item if specified on input |
| createdAt   | Number (unix timestamp) | The creation date of the created Item    |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the created Item |
| Inventories | JSON dictionary         | All inventory quantities per Warehouse (based on warehouseID) that exist for the created Item |

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
    "price": 25,
    "Inventories": [{
      "id": 5,
      "sku": "hk-001",
      "warehouseID": "w-SF",
      "quantity": 14
    }]
  }
]
```

Retrieves the details of existing Items based on the search parameters given in the request.

Returns an **array** of retrieved Item objects if successful.

### HTTP Request

`GET https://api.withhako.com/v1/items`

### Query Parameters
_This endpoint requires a JSON object as input. The parameters for the object are as follows:_

| Parameter | Type            | Required |
| --------- | --------------- | -------- |
| sku       | String          | false    |
| name      | String          | false    |
| expires   | JSON dictionary | false    |
| createdAt | JSON dictionary | false    |
| updatedAt | JSON dictionary | false    |
| price     | JSON dictionary | false    |
| inventory | JSON dictionary | false    |

### Inventory Parameter Options

_This parameter takes a JSON dictionary as input, with at least one or more of the following parameters in the Inventory query parameters. For example, searching by warehouseID will retrieve all the specified Item in the specified Warehouse, while searching by quantity will search for all Warehouses with at least "quantity" Items._

| Parameter   | Type   |
| ----------- | ------ |
| warehouseID | String |
| quantity    | Number |

### Expires Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### CreatedAt Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### UpdatedAt Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### Price Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| sku         | String                  | The SKU of the retrieved Item            |
| name        | String                  | The name of the retrieved Item if exists |
| expires     | Number (unix timestamp) | The expiration date of the retrieved Item if exists |
| price       | Number                  | The price of the retrieved Item if exists |
| createdAt   | Number (unix timestamp) | The creation date of the retrieved Item  |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the retrieved Item |
| Inventories | JSON dictionary         | All inventory quantities per Warehouse (based on warehouseID) that exist for the retrieved Item |

##Update Items
```shell
curl -X PATCH -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "updates": {"name": "Men\"s Long Sleeve Tshirt", "inventory": [{"warehouseID": "w-SF", "quantity": 100}]}}]' "https://api.withhako.com/v1/items"
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
    "price": 25,
    "Inventories": [{
      "warehouseID": "w-SF",
      "quantity": 100
    }]
  }
]
```

Updates Item objects by setting the values of the parameters passed. Any parameters not provided will be left unchanged. 

Returns an **array** of updated Item objects if successful.

### HTTP Request

`PATCH https://api.withhako.com/v1/items`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter | Type            | Required |
| --------- | --------------- | -------- |
| sku       | String          | true     |
| updates   | JSON dictionary | true     |

### Update Parameter Options
| Parameter | Type                    |
| --------- | ----------------------- |
| sku       | String                  |
| name      | String                  |
| expires   | Number (unix timestamp) |
| price     | Number                  |
| inventory | Array                   |

### Inventory Parameter Options

Updating Inventory with this endpoint will **override** any existing data for the given parameters, such as manually setting the inventory quantity to 0 (e.g., resolving a inventory discrepancy). This endpoint should not be used for regular inventory quantity updating. For regular quantity updating, see Incrementing Inventory or Decrementing Inventory. You can use this Update endpoint to instantiate Inventory quantities if you did not do so when you first created the Item.

<aside class="notice">
Important! A warehouse with the specified warehouseID must exist before you can update Items with inventory or you will receive an error as the response. You should create your warehouses before updating Items with inventory.
</aside>

_This endpoint expects an **array** of objects, which should follow the convention below:_

| Parameter   | Type   |
| ----------- | ------ |
| warehouseID | String |
| quantity    | Number |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| sku         | String                  | The SKU of the updated Item              |
| name        | String                  | The name of the updated Item if exists   |
| expires     | Number (unix timestamp) | The expiration date of the updated Item if exists |
| price       | Number                  | The price of the updated Item if exists  |
| createdAt   | Number (unix timestamp) | The creation date of the updated Item    |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the updated Item |
| Inventories | JSON dictionary         | All inventory quantities per Warehouse (based on warehouseID) that exist for the updated Item |

## Incrementing Item Inventory

```shell
curl -X PATCH -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "warehouseID": "warehouse-010", "incrementBy": 2}]' "https://api.withhako.com/v1/items/inventory/increment"
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
    "price": 25,
    "Inventories": [{
      "warehouseID": "w-SF",
      "quantity": 16
    }]
  }
]
```

Updates existing Items' quantities. **This endpoint should be used in most cases when updating inventory numbers**, such as after receiving more stock of an item from a vendor.

Returns an **array** of updated Item objects if successful.

### HTTP Request

`PATCH https://api.withhako.com/v1/items/inventory/increment`

### Query Parameters

_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| sku         | String | true     |
| warehouseID | String | true     |
| incrementBy | Number | true     |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| sku         | String                  | The SKU of the updated Item              |
| name        | String                  | The name of the updated Item if exists   |
| expires     | Number (unix timestamp) | The expiration date of the updated Item if exists |
| price       | Number                  | The price of the updated Item if exists  |
| createdAt   | Number (unix timestamp) | The creation date of the updated Item    |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the updated Item |
| Inventories | JSON dictionary         | All inventory quantities per Warehouse (based on warehouseID) that exist for the updated Item |

## Decrementing Item Inventory

```shell
curl -X PATCH -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "warehouseID": "warehouse-010", "decrementBy": 2}]' "https://api.withhako.com/v1/items/inventory/decrement"
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
    "price": 25,
    "Inventories": [{
      "warehouseID": "w-SF",
      "quantity": 12
    }]
  }
]
```

Updates existing Items' quantities. **This endpoint should be used in most cases when updating inventory numbers**, such as after selling an item to a customer.

Returns an **array** of updated Item objects if successful.

### HTTP Request

`PATCH https://api.withhako.com/v1/items/inventory/decrement`

### Query Parameters

_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| sku         | String | true     |
| warehouseID | String | true     |
| decrementBy | Number | true     |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| sku         | String                  | The SKU of the updated Item              |
| name        | String                  | The name of the updated Item if exists   |
| expires     | Number (unix timestamp) | The expiration date of the updated Item if exists |
| price       | Number                  | The price of the updated Item if exists  |
| createdAt   | Number (unix timestamp) | The creation date of the updated Item    |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the updated Item |
| Inventories | JSON dictionary         | All inventory quantities per Warehouse (based on warehouseID) that exist for the updated Item |

## Transferring Item Inventory

```shell
curl -X PATCH -H 'Content-Type: application/json' --data '[{"sku": "hk-001", "sourceWarehouseID": "warehouse-010", "destinationWarehouseID": "warehouse-011", "quantity": 25}]' "https://api.withhako.com/v1/items/inventory/transfer"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "hk-001",
    "warehouseID": "warehouse-010",
    "quantity": 12
  },
  {
    "id": 2,
    "sku": "hk-001",
    "warehouseID": "warehouse-011",
    "quantity": 37
  }
]
```

Transfers existing Item inventory from one warehouse (**sourceWarehouseID**) to another (**destinationWarehouseID**). One use case of transferring inventory is from the main warehouse to a storefront.

<aside class="notice">
There must be at least the quantity specified in the warehouse with sourceWarehouseID in order to successfully call this endpoint. Additionally, SKU, sourceWarehouseID, and destinationWarehouseID must already exist as Items and Warehouses.
</aside>

Returns an **array** of updated Item objects if successful.

### HTTP Request

`PATCH https://api.withhako.com/v1/items/inventory/transfer`

### Query Parameters

_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter              | Type   | Required |
| ---------------------- | ------ | -------- |
| sku                    | String | true     |
| sourceWarehouseID      | String | true     |
| destinationWarehouseID | String | true     |
| quantity               | Number | true     |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| sku         | String                  | The SKU of the updated Item              |
| name        | String                  | The name of the updated Item if exists   |
| expires     | Number (unix timestamp) | The expiration date of the updated Item if exists |
| price       | Number                  | The price of the updated Item if exists  |
| createdAt   | Number (unix timestamp) | The creation date of the updated Item    |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the updated Item |
| Inventories | JSON dictionary         | All inventory quantities per Warehouse (based on warehouseID) that exist for the updated Item |

##Delete Items

```shell
curl -X DELETE -H 'Content-Type: application/json' --data '[{"sku": "hk-001"}]' "https://api.withhako.com/v1/items"
  -u "your_API_key:"
```

> Example Response:

```json
["hk-001"]
```

Permanently deletes Item objects. This cannot be undone.

Returns an **array** of deleted Item SKUs if successful.

### HTTP Request

`DELETE https://api.withhako.com/v1/items`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter | Type   | Required |
| --------- | ------ | -------- |
| sku       | String | true     |

### Response Parameters
_This endpoint returns an **array** of one or more strings upon success. The parameters for the returned strings are as follows:_

| Parameter | Type   | Description                 |
| --------- | ------ | --------------------------- |
| sku       | String | The SKU of the deleted Item |

# Bundles
## The Bundle Object
The **Bundle** object represents a collection of **Items**. The Bundle object can be used to represent a variety of product groupings. For example, a customer might sell a gift collection as a Bundle, which includes a variety of Item products. Or, a Bundle might represent a finished, assembled product, while the Items represent the raw materials of the Bundle.

###Attributes
| Attribute | Type   | Description                              |
| --------- | ------ | ---------------------------------------- |
| sku       | String | The unique SKU of the Bundle             |
| name      | String | The name of the Bundle                   |
| item_skus | Array  | Array of the Item SKUs that comprise the Bundle |
| price     | Number | The price of the Bundle                  |

##Create Bundles
```shell
curl -X POST -H 'Content-Type: application/json' --data '[{"sku": "bundle-001", "name": "Gift Collection", "item_skus": [{"sku": "item-sku-001", "quantity": 15}]}]' "https://api.withhako.com/v1/bundles"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "bundle-001",
    "name": "Gift Collection",
    "item_skus": [
      {
        "sku": "item-sku-001",
        "quantity": 15
      }
    ]
  }
]
```

Creates **Bundle** objects with the specified SKU and any other optional parameters provided. SKU must be unique to all other existing Bundles.

Returns an **array** of created Bundle objects if successful.

### HTTP Request

`POST https://api.withhako.com/v1/bundles`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| warehouseID | String | true     |
| name        | String | false    |
| item_skus   | Array  | false    |
| price       | Number | false    |

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| sku       | String                  | The SKU of the created Bundle            |
| name      | String                  | The name of the created Bundle           |
| createdAt | Number (unix timestamp) | The creation date of the created Bundle  |
| updatedAt | Number (unix timestamp) | The most recent updated date of the created Bundle |
| item_skus | Array                   | The Item SKUs that comprise the Bundle, if specified |
| price     | Number                  | The price of the Bundle, if specified    |

##Retrieve Bundles
```shell
curl -X GET -H 'Content-Type: application/json' "https://api.withhako.com/v1/bundles"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "bundle-001",
    "name": "Gift Collection",
    "item_skus": [
      {
        "sku": "item-sku-001",
        "quantity": 15
      }
    ]
  }
]
```

Retrieves the details of existing Bundles based on the search parameters given in the request.

Returns an **array** of retrieved Bundle objects if successful.

### HTTP Request

`GET https://api.withhako.com/v1/bundles`

### Query Parameters
_This endpoint requires a JSON object as input. The parameters for the object are as follows:_

| Parameter | Type            | Required |
| --------- | --------------- | -------- |
| sku       | String          | false    |
| name      | String          | false    |
| createdAt | JSON dictionary | false    |
| updatedAt | JSON dictionary | false    |
| price     | Number          | false    |

### CreatedAt Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### UpdatedAt Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### Price Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| sku       | String                  | The SKU of the retrieved Bundle          |
| name      | String                  | The name of the retrieved Bundle         |
| createdAt | Number (unix timestamp) | The creation date of the retrieved Bundle |
| updatedAt | Number (unix timestamp) | The most recent updated date of the retrieved Bundle |
| item_skus | Array                   | All Item SKUs that comprise the Bundle   |
| price     | Number                  | The price of the Bundle                  |

##Update Bundles
```shell
curl -X PATCH -H 'Content-Type: application/json' --data '[{"sku": "bundle-001", "updates": {"name": "Limited Time Gift Collection", "item_skus": [{"sku": "item-sku-001", "quantity": 25}]}}]' "https://api.withhako.com/v1/bundles"
  -u "your_API_key:"
```

> Example Response:

```json
[
  {
    "id": 1,
    "sku": "bundle-001",
    "name": "Limited Time Gift Collection",
    "item_skus": [
      {
        "sku": "item-sku-001",
        "quantity": 25
      }
    ]
  }
]
```

Updates Bundle objects by setting the values of the parameters passed. Any parameters not provided will be left unchanged. 

Returns an **array** of updated Bundle objects if successful.

### HTTP Request

`PATCH https://api.withhako.com/v1/bundles`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter | Type            | Required |
| --------- | --------------- | -------- |
| sku       | String          | true     |
| updates   | JSON dictionary | true     |

### Update Parameter Options
| Parameter | Type   |
| --------- | ------ |
| sku       | String |
| name      | String |
| item_skus | Array  |
| price     | Number |

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| sku       | String                  | The SKU of the updated Bundle            |
| name      | String                  | The name of the updated Bundle           |
| createdAt | Number (unix timestamp) | The creation date of the updated Bundle  |
| updatedAt | Number (unix timestamp) | The most recent updated date of the updated Bundle |
| item_skus | Array                   | The Items that comprise the updated Bundle |
| price     | Number                  | The price of the updated Bundle          |

##Delete Bundles
```shell
curl -X DELETE -H 'Content-Type: application/json' --data '[{"sku": "bundle-001"}]' "https://api.withhako.com/v1/bundles"
  -u "your_API_key:"
```

> Example Response:

```json
["bundle-001"]
```

Permanently deletes Bundle objects. This cannot be undone.

Returns an **array** of deleted Bundle SKUs if successful.

### HTTP Request

`DELETE https://api.withhako.com/v1/bundles`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter | Type   | Required |
| --------- | ------ | -------- |
| sku       | String | true     |

### Response Parameters
_This endpoint returns an array of one or more strings upon success. The parameters for the returned strings are as follows:_

| Parameter | Type   | Description                   |
| --------- | ------ | ----------------------------- |
| sku       | String | The SKU of the deleted Bundle |

# Warehouses
## The Warehouse Object
The **Warehouse** object represents locations where **Items** reside. For example, a customer's ecommerce store may sell t-shirts and have two Warehouses where product is kept.

###Attributes
| Attribute   | Type   | Description                              |
| ----------- | ------ | ---------------------------------------- |
| name        | String | The name of the Warehouse                |
| warehouseID | String | A unique string identifying the Warehouse |

##Create Warehouses
```shell
curl -X POST -H 'Content-Type: application/json' --data '[{"warehouseID": "warehouse-010", "name": "San Francisco Warehouse"}]' "https://api.withhako.com/v1/warehouses"
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

`POST https://api.withhako.com/v1/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| warehouseID | String | true     |
| name        | String | false    |

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| warehouseID | String                  | The warehouseID of the created Warehouse |
| name        | String                  | The name of the created Warehouse        |
| createdAt   | Number (unix timestamp) | The creation date of the created Warehouse |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the created Warehouse |

##Retrieve Warehouses
```shell
curl -X GET -H 'Content-Type: application/json' "https://api.withhako.com/v1/warehouses"
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

`GET https://api.withhako.com/v1/warehouses`

### Query Parameters
_This endpoint requires a JSON object as input. The parameters for the object are as follows:_

| Parameter   | Type            | Required |
| ----------- | --------------- | -------- |
| warehouseID | String          | false    |
| name        | String          | false    |
| createdAt   | JSON dictionary | false    |
| updatedAt   | JSON dictionary | false    |

### CreatedAt Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### UpdatedAt Parameter Options

_This parameter takes a JSON dictionary as input, with one or two of the following. Note that gte cannot be combined with gt, nor lte with lt.:_

| Parameter | Type                    | Description                              |
| --------- | ----------------------- | ---------------------------------------- |
| gte       | Number (unix timestamp) | Greater than or equal to a unix timestamp |
| gt        | Number (unix timestamp) | Greater than a unix timestamp            |
| lte       | JSON dictionary         | Less than or equal to a unix timestamp   |
| lt        | JSON dictionary         | Less than a unix timestamp               |

### Response Parameters

_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| warehouseID | String                  | The warehouseID of the retrieved Warehouse |
| name        | String                  | The name of the retrieved Warehouse      |
| createdAt   | Number (unix timestamp) | The creation date of the retrieved Warehouse |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the retrieved Warehouse |
| Inventories | JSON dictionary         | All inventory quantities Item (based on sku) that exist for the retrieved Warehouse |

##Update Warehouses
```shell
curl -X PATCH -H 'Content-Type: application/json' --data '[{"warehouseID": "warehouse-010", "updates": {"name": "Bayview Warehouse"}}]' "https://api.withhako.com/v1/warehouses"
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

`PATCH https://api.withhako.com/v1/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter   | Type            | Required |
| ----------- | --------------- | -------- |
| warehouseID | String          | true     |
| updates     | JSON dictionary | true     |

### Update Parameter Options
| Parameter   | Type   |
| ----------- | ------ |
| warehouseID | String |
| name        | String |

### Response Parameters
_This endpoint returns an **array** of one or more objects upon success. The parameters for the returned objects are as follows:_

| Parameter   | Type                    | Description                              |
| ----------- | ----------------------- | ---------------------------------------- |
| warehouseID | String                  | The warehouseID of the updated Warehouse |
| name        | String                  | The name of the updated Warehouse        |
| createdAt   | Number (unix timestamp) | The creation date of the updated Warehouse |
| updatedAt   | Number (unix timestamp) | The most recent updated date of the updated Warehouse |

##Delete Warehouses
```shell
curl -X DELETE -H 'Content-Type: application/json' --data '[{"warehouseID": "warehouse-010"}]' "https://api.withhako.com/v1/warehouses"
  -u "your_API_key:"
```

> Example Response:

```json
["warehouse-010"]
```

Permanently deletes Warehouse objects. This cannot be undone.

Returns an **array** of deleted Warehouse warehouseIDs if successful.

### HTTP Request

`DELETE https://api.withhako.com/v1/warehouses`

### Query Parameters
_This endpoint requires an **array** of one or more objects as input. The parameters for the objects are as follows:_

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| warehouseID | String | true     |

### Response Parameters
_This endpoint returns an array of one or more strings upon success. The parameters for the returned strings are as follows:_

| Parameter   | Type   | Description                              |
| ----------- | ------ | ---------------------------------------- |
| warehouseID | String | The warehouseID of the deleted Warehouse |


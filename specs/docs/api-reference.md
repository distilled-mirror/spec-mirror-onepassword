> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Connect Server API reference

> [!NOTE]
>
> If you're new to 1Password Secrets Automation and 1Password Connect Server, [learn how to get started with a Secrets Automation workflow](/connect/get-started/).

You can use the Connect API to work with the vaults and items in your account, and to list API activity on a Connect server:

* [List vaults](#list-vaults)
* [Get vault details](#get-vault-details)
* [List items](#list-items)
* [Add an item](#add-an-item)
* [Get item details](#get-item-details)
* [Replace an item](#replace-an-item)
* [Delete an item](#delete-an-item)
* [Update a subset of item attributes](#update-a-subset-of-item-attributes)
* [List files](#list-files)
* [Get file details](#get-file-details)
* [Get file content](#get-file-content)
* [List API activity](#list-api-activity)

To view the API in another tool, download the [1Password Connect API specification file (1.8.1)](https://i.1password.com/media/1password-connect/1password-connect-api_1.8.1.yaml).

## Requirements

Before you can use the 1Password Connect Server API, you'll need to:

* [Sign up for 1Password.](https://1password.com/pricing/password-manager)
* [Set up a Secrets Automation workflow.](/connect/get-started/#step-1).
* [Deploy 1Password Connect](/connect/get-started/#step-2-deploy-1password-connect-server) in your infrastructure.

## Request headers

Each request to the API has to be authenticated with an [access token](/connect/manage-connect/#manage-access-tokens). Provide it and specify the content type:

```
Authorization: Bearer <access_token>
Content-type: application/json
```

## List vaults

```
GET /v1/vaults
```

### Path parameters

No path parameters

### Query parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`filter`</th>
      <td>string</td>

      <td>
        Filter the vault collection using [SCIM-style filters](https://bookstack.soffid.com/books/scim/page/scim-query-syntax). Vaults can only be filtered by `name`. *Optional.*

        For example: `name eq "Demo Vault"`
      </td>
    </tr>
  </tbody>
</table>

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an array of vault names and IDs</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
</dl>

## Get vault details

```
GET /v1/vaults/{vaultUUID}
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to retrieve items from.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns a [Vault object](#vault-object)</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>Vault not found</dd>
</dl>

## List items

```
GET /v1/vaults/{vaultUUID}/items
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to get the details of.</td>
    </tr>
  </tbody>
</table>

### Query parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`filter`</th>
      <td>string</td>

      <td>
        Filter the item collection using [SCIM-style filters ](https://bookstack.soffid.com/books/scim/page/scim-query-syntax). Items can only be filtered by `title` or `tag`. *Optional.*

        For example: `title eq "Example Item"` or `tag eq "banking"`
      </td>
    </tr>
  </tbody>
</table>

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an array of [Item objects](#item-object) that don't include sections and fields</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>404</dt>
  <dd>Vault not found</dd>
</dl>

## Add an item

```
POST /v1/vaults/{vaultUUID}/items
```

The request must include a FullItem object, containing the information to create the item. For example:

```json theme={null}
{
	"vault": {
	"id": "ftz4pm2xxwmwrsd7rjqn7grzfz"
	},
	"title": "Secrets Automation Item",
	"category": "LOGIN",
	"tags": [
	"connect",
	"\ud83d\udc27"
	],
	"sections": [
	{
		"label": "Security Questions",
		"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
	}
	],
	"fields": [
	{
		"value": "wendy",
		"purpose": "USERNAME"
	},
	{
		"purpose": "PASSWORD",
		"generate": true,
		"recipe":
		{
			"length": 55,
			"characterSets": [
			"LETTERS",
			"DIGITS"
			]
		}
	},
	{
		"section": {
		"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
		},
		"type": "CONCEALED",
		"generate": true,
		"label": "Recovery Key"
	},
	{
		"section": {
		"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
		},
		"type": "STRING",
		"generate": true,
		"label": "Random Text"
	},
	{
		"type": "URL",
		"label": "Example",
		"value": "https://example.com"
	}
	]
}
```

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`title`</th>
      <td>string</td>
      <td>The title of the item.</td>
    </tr>

    <tr>
      <th>`vault`</th>
      <td>object</td>
      <td>An object containing an `id` property whose value is the UUID of the vault the item is in.</td>
    </tr>

    <tr>
      <th>`category`</th>
      <td>string</td>

      <td>
        The category of the item. One of:

        <ul>
          <li>`"LOGIN"`</li>
          <li>`"PASSWORD"`</li>
          <li>`"API_CREDENTIAL"`</li>
          <li>`"SERVER"`</li>
          <li>`"DATABASE"`</li>
          <li>`"CREDIT_CARD"`</li>
          <li>`"MEMBERSHIP"`</li>
          <li>`"PASSPORT"`</li>
          <li>`"SOFTWARE_LICENSE"`</li>
          <li>`"OUTDOOR_LICENSE"`</li>
          <li>`"SECURE_NOTE"`</li>
          <li>`"WIRELESS_ROUTER"`</li>
          <li>`"BANK_ACCOUNT"`</li>
          <li>`"DRIVER_LICENSE"`</li>
          <li>`"IDENTITY"`</li>
          <li>`"REWARD_PROGRAM"`</li>
          <li>`"EMAIL_ACCOUNT"`</li>
          <li>`"SOCIAL_SECURITY_NUMBER"`</li>
          <li>`"MEDICAL_RECORD"`</li>
          <li>`"SSH_KEY"`</li>
        </ul>

        You can't create items using the "CUSTOM" or "DOCUMENT" categories.
      </td>
    </tr>

    <tr>
      <th>`urls`</th>
      <td>array</td>
      <td>Array of [URL objects](#item-url-object) containing URLs for the item.</td>
    </tr>

    <tr>
      <th>`favorite`</th>
      <td>boolean</td>
      <td>Mark the item as a favorite.</td>
    </tr>

    <tr>
      <th>`tags`</th>
      <td>string</td>
      <td>An array of strings of the tags assigned to the item.</td>
    </tr>

    <tr>
      <th>`fields`</th>
      <td>array</td>
      <td>An array of [Field objects](#item-field-object) of the fields to include with the item.</td>
    </tr>

    <tr>
      <th>`sections`</th>
      <td>array</td>
      <td>An array of [Section objects](#item-section-object) of the sections to include with the item.</td>
    </tr>
  </tbody>
</table>

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to create an item in.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns [Item object](#item-object) containing the new item</dd>
  <dt>400</dt>
  <dd>Unable to create item due to invalid input</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>Item not found</dd>
</dl>

## Get item details

```
GET /v1/vaults/{vaultUUID}/items/{itemUUID}
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to retrieve the item from.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to retrieve.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an [Item object](#item-object)</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>Item not found</dd>
</dl>

## Replace an item

```
PUT /v1/vaults/{vaultUUID}/items/{itemUUID}
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to retrieve the item from.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to replace.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an [Item object](#item-object)</dd>
  <dt>400</dt>
  <dd>Unable to create item due to invalid input</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>Item not found</dd>
</dl>

## Delete an item

```
DELETE /v1/vaults/{vaultUUID}/items/{itemUUID}
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to retrieve the item from.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to delete.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>204</dt>
  <dd>Successfully deleted an item</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>Item not found</dd>
</dl>

## Update a subset of item attributes

```
PATCH /v1/vaults/{vaultUUID}/items/{itemUUID}
```

Applies an `add`, `remove`, or `replace` operation on an item or the fields of an item. Uses the [RFC6902 JSON Patch](https://tools.ietf.org/html/rfc6902) document standard.

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`op`</th>
      <td>string</td>

      <td>
        The kind of operation to perform. One of:

        <ul>
          <li>`add`</li>
          <li>`remove`</li>
          <li>`replace`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`path`</th>
      <td>string</td>
      <td>An RFC6901 JSON Pointer to the item, an item attribute, an item field by field ID, or an item field attribute. For example: `"/fields/vy09gd8EXAMPLE/label"`</td>
    </tr>

    <tr>
      <th>`value`</th>
      <td>any</td>
      <td>The new value to apply at the path.</td>
    </tr>
  </tbody>
</table>

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault the item is in.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to update.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an [Item object](#item-object) of the updated item.</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>Item not found</dd>
</dl>

## List files

```
GET /v1/vaults/{vaultUUID}/items/{itemUUID}/files
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to get the details of.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to retrieve.</td>
    </tr>
  </tbody>
</table>

### Query parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`inline_content`</th>
      <td>boolean</td>
      <td>Whether to return the Base-64 encoded file content. The file size must be less than `OP_MAX_INLINE_FILE_SIZE_KB`, or 100 kilobytes if the file size isn't defined. *Optional.*</td>
    </tr>
  </tbody>
</table>

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an array of [File objects](#item-file-object)</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>404</dt>
  <dd>Item not found</dd>
  <dt>413</dt>
  <dd>File too large to display inline</dd>
</dl>

## Get File details

```
GET /v1/vaults/{vaultUUID}/items/{itemUUID}/files/{fileUUID}
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to retrieve the item from.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to retrieve the file from.</td>
    </tr>

    <tr>
      <th>`fileUUID`</th>
      <td>string</td>
      <td>The UUID of the file to retrieve.</td>
    </tr>
  </tbody>
</table>

### Query parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`inline_content`</th>
      <td>boolean</td>
      <td>Whether to return the Base-64 encoded file content. The file size must be less than `OP_MAX_INLINE_FILE_SIZE_KB`, or 100 kilobytes if the file size isn't defined. *Optional.*</td>
    </tr>
  </tbody>
</table>

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns a [File object](#item-file-object)</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>File not found</dd>
  <dt>413</dt>
  <dd>File too large to display inline</dd>
</dl>

## Get file content

```
GET /v1/vaults/{vaultUUID}/items/{itemUUID}/files/{fileUUID}/content
```

### Path parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`vaultUUID`</th>
      <td>string</td>
      <td>The UUID of the vault to retrieve the item from.</td>
    </tr>

    <tr>
      <th>`itemUUID`</th>
      <td>string</td>
      <td>The UUID of the item to retrieve the file from.</td>
    </tr>

    <tr>
      <th>`fileUUID`</th>
      <td>string</td>
      <td>The UUID of the file to retrieve.</td>
    </tr>
  </tbody>
</table>

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns the content of the file</dd>
  <dt>401</dt>
  <dd>Invalid or missing token</dd>
  <dt>403</dt>
  <dd>Unauthorized access</dd>
  <dt>404</dt>
  <dd>File not found</dd>
</dl>

## List API activity

```
GET /v1/activity
```

Retrieve a list of API Requests that have been made.

### Query parameters

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`limit`</th>
      <td>integer</td>
      <td>How many API Events should be retrieved in a single request. *Optional.*</td>
    </tr>

    <tr>
      <th>`offset`</th>
      <td>integer</td>
      <td>How far into the collection of API Events should the response start. *Optional.*</td>
    </tr>
  </tbody>
</table>

## Server Heartbeat

```
GET /heartbeat
```

Simple "ping" endpoint to check whether server is active.

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns a `text/plain` response with a single "."</dd>
</dl>

## Server Health

```
GET /health
```

Query the state of the server and its service dependencies.

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns a [Server Health object](#server-health-object)</dd>
</dl>

## Metrics

```
GET /metrics
```

Returns Prometheus metrics collected by the server.

### Query parameters

No query parameters.

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns a plaintext list of Prometheus metrics. See the [Prometheus documentation ](https://prometheus.io/docs/instrumenting/exposition_formats/#text-based-format) for specifics.</dd>
</dl>

## Response object models

### APIRequest object

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`requestID`</th>
      <td>string</td>
      <td>The UUID for the request.</td>
    </tr>

    <tr>
      <th>`timestamp`</th>
      <td>dateTime</td>
      <td>Date and time of the request.</td>
    </tr>

    <tr>
      <th>`action`</th>
      <td>string</td>

      <td>
        The action taken. One of:

        <ul>
          <li>`"READ"`</li>
          <li>`"CREATE"`</li>
          <li>`"UPDATE"`</li>
          <li>`"DELETE"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`result`</th>
      <td>string</td>

      <td>
        The result of the action. One of:

        <ul>
          <li>`"SUCCESS"`</li>
          <li>`"DENY"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`actor`</th>
      <td>object</td>
      <td>An [Actor object](#apirequest-actor-object).</td>
    </tr>

    <tr>
      <th>`resource`</th>
      <td>object</td>
      <td>A [Resource object](#apirequest-resource-object).</td>
    </tr>
  </tbody>
</table>

#### APIRequest: Actor object

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`id`</th>
      <td>string</td>
      <td>The UUID of the Connect server that made the request.</td>
    </tr>

    <tr>
      <th>`account`</th>
      <td>string</td>
      <td>The UUID of the 1Password account the request went to.</td>
    </tr>

    <tr>
      <th>`jti`</th>
      <td>string</td>
      <td>The UUID of the access token used to authenticate the request.</td>
    </tr>

    <tr>
      <th>`userAgent`</th>
      <td>string</td>
      <td>The user agent string specified in the request.</td>
    </tr>

    <tr>
      <th>`ip`</th>
      <td>string</td>
      <td>The IP address the request originated from.</td>
    </tr>
  </tbody>
</table>

#### APIRequest: Resource object

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`type`</th>
      <td>string</td>

      <td>
        The resource requested. One of:

        <ul>
          <li>`"ITEM"`</li>
          <li>`"VAULT"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`vault`</th>
      <td>object</td>
      <td>An object containing an `id` property with the value of the UUID of the vault requested.</td>
    </tr>

    <tr>
      <th>`item`</th>
      <td>object</td>
      <td>An object containing an `id` property with the value of the UUID of the item requested.</td>
    </tr>

    <tr>
      <th>`itemVersion`</th>
      <td>integer</td>
      <td>The version of the item.</td>
    </tr>
  </tbody>
</table>

### ErrorResponse object

```json theme={null}
{
	status: 401,
	message: "Invalid or missing token"
}
```

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`status`</th>
      <td>integer</td>
      <td>The HTTP status code.</td>
    </tr>

    <tr>
      <th>`message`</th>
      <td>string</td>
      <td>A message detailing the error.</td>
    </tr>
  </tbody>
</table>

### Vault object

```json theme={null}
{
	"id": "ytrfte14kw1uex5txaore1emkz",
	"name": "Demo",
	"attributeVersion": 1,
	"contentVersion": 72,
	"items": 7,
	"type": "USER_CREATED",
	"createdAt": "2021-04-10T17:34:26Z",
	"updatedAt": "2021-04-13T14:33:50Z"
}
```

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`id`</th>
      <td>string</td>
      <td>The UUID of the vault.</td>
    </tr>

    <tr>
      <th>`name`</th>
      <td>string</td>
      <td>The name of the vault.</td>
    </tr>

    <tr>
      <th>`description`</th>
      <td>string</td>
      <td>The description for the vault.</td>
    </tr>

    <tr>
      <th>`attributeVersion`</th>
      <td>integer</td>
      <td>The version of the vault metadata.</td>
    </tr>

    <tr>
      <th>`contentVersion`</th>
      <td>integer</td>
      <td>The version of the vault contents.</td>
    </tr>

    <tr>
      <th>`items`</th>
      <td>integer</td>
      <td>Number of active items in the vault.</td>
    </tr>

    <tr>
      <th>`type`</th>
      <td>string</td>

      <td>
        The type of vault. One of:

        <ul>
          <li>`"EVERYONE"`: The team Shared vault.</li>
          <li>`"PERSONAL"`: The Private vault for the Connect server.</li>
          <li>`"USER_CREATED"`: A vault created by a user.</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`createdAt`</th>
      <td>dateTime</td>
      <td>Date and time when the vault was created.</td>
    </tr>

    <tr>
      <th>`updatedAt`</th>
      <td>dateTime</td>
      <td>Date and time when the vault or its contents were last changed.</td>
    </tr>
  </tbody>
</table>

### Item object

```json theme={null}
{
	"id": "2fcbqwe9ndg175zg2dzwftvkpa",
	"title": "Secrets Automation Item",
	"tags": [
		"connect",
		"\ud83d\udc27"
	],
	"vault": {
		"id": "ftz4pm2xxwmwrsd7rjqn7grzfz"
	},
	"category": "LOGIN",
	"sections": [
		{
			"id": "95cdbc3b-7742-47ec-9056-44d6af82d562",
			"label": "Security Questions"
		}
	],
	"fields": [
		{
			"id": "username",
			"type": "STRING",
			"purpose": "USERNAME",
			"label": "username",
			"value": "wendy"
		},
		{
			"id": "password",
			"type": "CONCEALED",
			"purpose": "PASSWORD",
			"label": "password",
			"value": "mjXehR*uCj!aoe!iktt9KMtWb",
			"entropy": 148.0838165283203,
			"passwordDetails": {
				"entropy": 148,
				"generated": true,
				"strength": "FANTASTIC",
				"history": [
					"U-Trxf98hT_GTgNmA.a!pQp3U",
					"_ciCJpYuCB*E7@oRTto4JB4.3"
				]
			}
		},
		{
			"id": "notesPlain",
			"type": "STRING",
			"purpose": "NOTES",
			"label": "notesPlain"
		},
		{
			"id": "a6cvmeqakbxoflkgmor4haji7y",
			"type": "URL",
			"label": "Example",
			"value": "https://example.com"
		},
		{
			"id": "boot3vsxwhuht6g7cmcx4m6rcm",
			"section": {
				"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
			},
			"type": "CONCEALED",
			"label": "Recovery Key",
			"value": "s=^J@GhHP_isYP>LCq?vv8u7T:*wBP.c"
		},
		{
			"id": "axwtgyjrvwfp5ij7mtkw2zvijy",
			"section": {
				"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
			},
			"type": "STRING",
			"label": "Random Text",
			"value": "R)D~KZdV!8?51QoCibDUse7=n@wKR_}]"
		}
	],
	"files": [
	{
		"id": "6r65pjq33banznomn7q22sj44e",
		"name": "testfile.txt",
		"size": 35,
		"content_path": "v1/vaults/ftz4pm2xxwmwrsd7rjqn7grzfz/items/2fcbqwe9ndg175zg2dzwftvkpa/files/6r65pjq33banznomn7q22sj44e/content",
	},
	{
		"id": "oyez5gf6xjfptlhc3o4n6o6hvm",
		"name": "samplefile.png",
		"size": 296639,
		"content_path": "v1/vaults/ftz4pm2xxwmwrsd7rjqn7grzfz/items/2fcbqwe9ndg175zg2dzwftvkpa/files/oyez5gf6xjfptlhc3o4n6o6hvm/content",
	}
	],
	"createdAt": "2021-04-10T17:20:05.98944527Z",
	"updatedAt": "2021-04-13T17:20:05.989445411Z"
}
```

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`id`</th>
      <td>string</td>
      <td>The UUID of the item.</td>
    </tr>

    <tr>
      <th>`title`</th>
      <td>string</td>
      <td>The title of the item.</td>
    </tr>

    <tr>
      <th>`vault`</th>
      <td>object</td>
      <td>An object containing an `id` property whose value is the UUID of the vault the item is in.</td>
    </tr>

    <tr>
      <th>`category`</th>
      <td>string</td>

      <td>
        The category of the item. One of:

        <ul>
          <li>`"LOGIN"`</li>
          <li>`"PASSWORD"`</li>
          <li>`"API_CREDENTIAL"`</li>
          <li>`"SERVER"`</li>
          <li>`"DATABASE"`</li>
          <li>`"CREDIT_CARD"`</li>
          <li>`"MEMBERSHIP"`</li>
          <li>`"PASSPORT"`</li>
          <li>`"SOFTWARE_LICENSE"`</li>
          <li>`"OUTDOOR_LICENSE"`</li>
          <li>`"SECURE_NOTE"`</li>
          <li>`"WIRELESS_ROUTER"`</li>
          <li>`"BANK_ACCOUNT"`</li>
          <li>`"DRIVER_LICENSE"`</li>
          <li>`"IDENTITY"`</li>
          <li>`"REWARD_PROGRAM"`</li>
          <li>`"DOCUMENT"`</li>
          <li>`"EMAIL_ACCOUNT"`</li>
          <li>`"SOCIAL_SECURITY_NUMBER"`</li>
          <li>`"MEDICAL_RECORD"`</li>
          <li>`"SSH_KEY"`</li>
        </ul>

        You can't create items using the "CUSTOM" or "DOCUMENT" categories.
      </td>
    </tr>

    <tr>
      <th>`urls`</th>
      <td>array</td>
      <td>Array of [URL objects](#item-url-object) containing URLs for the item.</td>
    </tr>

    <tr>
      <th>`favorite`</th>
      <td>boolean</td>
      <td>Whether the item is marked as a favorite.</td>
    </tr>

    <tr>
      <th>`tags`</th>
      <td>array</td>
      <td>An array of strings of the tags assigned to the item.</td>
    </tr>

    <tr>
      <th>`version`</th>
      <td>integer</td>
      <td>The version of the item.</td>
    </tr>

    <tr>
      <th>`createdAt`</th>
      <td>dateTime</td>
      <td>Date and time when the item was created.</td>
    </tr>

    <tr>
      <th>`updatedAt`</th>
      <td>dateTime</td>
      <td>Date and time when the vault or its contents were last changed.</td>
    </tr>

    <tr>
      <th>`lastEditedBy`</th>
      <td>string</td>
      <td>UUID of the account that last changed the item.</td>
    </tr>
  </tbody>
</table>

#### Item: Field object

```json theme={null}
{
	"section": {
		"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
	},
	"type": "STRING",
	"generate": true,
	"label": "Random Text"
}
```

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`purpose` or `type`</th>
      <td>string</td>

      <td>
        Use `purpose` for the username, password, and notes fields. Possible values:

        <ul>
          <li>`"USERNAME"`</li>
          <li>`"PASSWORD"`</li>
          <li>`"NOTES"`</li>
        </ul>

        Use `type` for all other fields. Possible values are:

        <ul>
          <li>`"STRING"`</li>
          <li>`"EMAIL"`</li>
          <li>`"CONCEALED"`</li>
          <li>`"URL"`</li>
          <li>`"OTP"` (format: `otpauth://`)</li>
          <li>`"DATE"` (format: `YYYY-MM-DD`)</li>
          <li>`"MONTH_YEAR"` (format: `YYYYMM` or `YYYY/MM`)</li>
          <li>`"MENU"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`value`</th>
      <td>string</td>
      <td>The value to save for the field.
      You can specify a `generate` field instead of `value` to create a password or other random information for the value.</td>
    </tr>

    <tr>
      <th>`generate`</th>
      <td>boolean</td>
      <td>Generate a password and save in the value for the field. By default, the password is a 32-characters long, made up of letters, numbers, and symbols. To customize the password, include a `recipe` field.</td>
    </tr>

    <tr>
      <th>`recipe`</th>
      <td>object</td>
      <td>A [GeneratorRecipe object](#item-generatorrecipe-object).</td>
    </tr>

    <tr>
      <th>`section`</th>
      <td>object</td>
      <td>An object containing the UUID of a section in the item.</td>
    </tr>
  </tbody>
</table>

#### Item: File object

```json theme={null}
{
	"id": "6r65pjq33banznomn7q22sj44e",
	"name": "testfile.txt",
	"size": 35,
	"content_path": "v1/vaults/ftz4pm2xxwmwrsd7rjqn7grzfz/items/2fcbqwe9ndg175zg2dzwftvkpa/files/6r65pjq33banznomn7q22sj44e/content",
	"content": "VGhlIGZ1dHVyZSBiZWxvbmdzIHRvIHRoZSBjdXJpb3VzLgo=",
	"section": {
		"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
	},
}
```

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`id`</th>
      <td>string</td>
      <td>The UUID of the file.</td>
    </tr>

    <tr>
      <th>`name`</th>
      <td>string</td>
      <td>The name of the file.</td>
    </tr>

    <tr>
      <th>`size`</th>
      <td>integer</td>
      <td>The size of the file in bytes.</td>
    </tr>

    <tr>
      <th>`content_path`</th>
      <td>string</td>
      <td>The path to download the contents of the file.</td>
    </tr>

    <tr>
      <th>`content`</th>
      <td>string</td>
      <td>The Base64-encoded contents of the file, if `inline_files` is set to `true`.</td>
    </tr>

    <tr>
      <th>`section`</th>
      <td>object</td>
      <td>An object containing the UUID of a section in the item.</td>
    </tr>
  </tbody>
</table>

#### Item: GeneratorRecipe object

The recipe is used in conjunction with the "generate" property to set the character set used to generate a new secure value.

```json theme={null}
{
	"length": 55,
	"characterSets": [
		"LETTERS",
		"DIGITS"
	]
}
```

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`length`</th>
      <td>integer</td>
      <td>The length of the password to generate. *Optional.*</td>
    </tr>

    <tr>
      <th>`characterSets`</th>
      <td>array</td>

      <td>
        An array containing of the kinds of characters to include. *Optional.* Possible values:

        <ul>
          <li>`"LETTERS"`</li>
          <li>`"DIGITS"`</li>
          <li>`"SYMBOLS"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`excludeCharacters`</th>
      <td>string</td>
      <td> A list of all characters that should be excluded from generated passwords. *Optional.*</td>
    </tr>
  </tbody>
</table>

#### Item: PasswordDetails object

```json theme={null}
{
    "entropy": 148,
    "generated": true,
    "strength": "FANTASTIC",
    "history": [
      "U-Trxf98hT_GTgNmA.a!pQp3U",
      "_ciCJpYuCB*E7@oRTto4JB4.3"
    ]
}
```

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`entropy`</th>
      <td>integer</td>
      <td>The unpredictability of the password, measured in bits.</td>
    </tr>

    <tr>
      <th>`generated`</th>
      <td>boolean</td>
      <td>Whether the password was generated using the password generator.</td>
    </tr>

    <tr>
      <th>`strength`</th>
      <td>string</td>

      <td>
        The strength of the password. One of:

        <ul>
          <li>`"TERRIBLE"`</li>
          <li>`"WEAK"`</li>
          <li>`"FAIR"`</li>
          <li>`"GOOD"`</li>
          <li>`"VERY_GOOD"`</li>
          <li>`"EXCELLENT"`</li>
          <li>`"FANTASTIC"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`history`</th>
      <td>array</td>
      <td>An array of strings containing the previous passwords of the item.</td>
    </tr>
  </tbody>
</table>

#### Item: Section object

```json theme={null}
{
	"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
	"label": "Security Questions",
}
```

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`id`</th>
      <td>string</td>
      <td>A unique identifier for the section.</td>
    </tr>

    <tr>
      <th>`label`</th>
      <td>string</td>
      <td>The label for the section.</td>
    </tr>
  </tbody>
</table>

#### Item: URL object

```json theme={null}
{
	"label": "website",
	"primary": true,
	"href": "https://example.com"
}
```

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`label`</th>
      <td>string</td>
      <td>The label for the URL.</td>
    </tr>

    <tr>
      <th>`primary`</th>
      <td>boolean</td>
      <td>Whether this is the primary URL for the item.</td>
    </tr>

    <tr>
      <th>`href`</th>
      <td>string</td>
      <td>The address.</td>
    </tr>
  </tbody>
</table>

### Server Health object

```json theme={null}
{
	"name": "1Password Connect API",
	"version": "1.2.1",
		"dependencies": [
			{
				"service": "sync",
				"status": "TOKEN_NEEDED"
			},
			{
				"service": "sqlite",
				"status": "ACTIVE",
				"message": "Connected to  ~/1password.sqlite"
			}
		]
}
```

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`name`</th>
      <td>string</td>
      <td>Name of the server</td>
    </tr>

    <tr>
      <th>`version`</th>
      <td>string</td>
      <td>Version info of the Connect server</td>
    </tr>

    <tr>
      <th>`dependencies`</th>
      <td>array</td>
      <td>An array of [Service Dependencies](#server-health-dependency-object).</td>
    </tr>
  </tbody>
</table>

#### Server Health: Dependency object

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <th>`service`</th>
      <td>string</td>
      <td>Name of the dependency</td>
    </tr>

    <tr>
      <th>`status`</th>
      <td>string</td>
      <td>The service's reported status</td>
    </tr>

    <tr>
      <th>`message`</th>
      <td>string</td>
      <td>Extra information about the dependency's status. *Optional.*</td>
    </tr>
  </tbody>
</table>


## Related topics

- [Get started with a 1Password Connect server](/connect/get-started.md)
- [About 1Password Connect Server security](/connect/security.md)
- [1Password Partnership API reference](/partnership-api/reference.md)

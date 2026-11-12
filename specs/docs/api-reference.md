> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Connect Server API reference

Use the Connect API to read and manage the vaults and items your Connect server can access, and to check the server's health and activity. You need a running [Connect server](https://www.1password.dev/connect/get-started.md) and an [access token](https://www.1password.dev/connect/manage-connect.md#manage-access-tokens).

> [card] [Try the API in your browser](https://www.1password.dev/connect/api-reference/get-vaults.md)
>
> Paste your token, enter your Connect server address, and send a real request from the docs.

## Endpoints

* [List vaults](https://www.1password.dev/connect/api-reference/get-vaults.md)
* [Get vault details](https://www.1password.dev/connect/api-reference/get-vault-by-id.md)
* [List items](https://www.1password.dev/connect/api-reference/get-vault-items.md)
* [Add an item](https://www.1password.dev/connect/api-reference/create-vault-item.md)
* [Get item details](https://www.1password.dev/connect/api-reference/get-vault-item-by-id.md)
* [Replace an item](https://www.1password.dev/connect/api-reference/update-vault-item.md)
* [Delete an item](https://www.1password.dev/connect/api-reference/delete-vault-item.md)
* [Update a subset of item attributes](https://www.1password.dev/connect/api-reference/patch-vault-item.md)
* [List files](https://www.1password.dev/connect/api-reference/get-item-files.md)
* [Get file details](https://www.1password.dev/connect/api-reference/get-item-file-by-id.md)
* [Get file content](https://www.1password.dev/connect/api-reference/download-item-file-by-id.md)
* [List API activity](https://www.1password.dev/connect/api-reference/get-api-activity.md)
* [Check the server heartbeat](https://www.1password.dev/connect/api-reference/get-heartbeat.md)
* [Check server health](https://www.1password.dev/connect/api-reference/get-server-health.md)
* [Get Prometheus metrics](https://www.1password.dev/connect/api-reference/get-prometheus-metrics.md)

To view the API in another tool, download the [1Password Connect API specification file (1.8.1)](https://i.1password.com/media/1password-connect/1password-connect-api_1.8.1.yaml).

## Requirements

Before you can use the 1Password Connect Server API, you'll need to:

* [Sign up for 1Password.](https://1password.com/pricing/password-manager)
* [Set up a Secrets Automation workflow.](https://www.1password.dev/connect/get-started.md#step-1).
* [Deploy 1Password Connect](https://www.1password.dev/connect/get-started.md#step-2-deploy-1password-connect-server) in your infrastructure.

## Request headers

Each request to the API has to be authenticated with an [access token](https://www.1password.dev/connect/manage-connect.md#manage-access-tokens). Provide it and specify the content type:

```text theme={null}
Authorization: Bearer <access_token>
Content-type: application/json
```

## Try it in the docs

Every endpoint has a **Try it** panel with its parameters, example requests and responses.

**1. Get an access token**

Create a token for your Connect server. See [Manage access tokens](https://www.1password.dev/connect/manage-connect.md#manage-access-tokens).

**2. Start with a read-only call**

Open [Get heartbeat](https://www.1password.dev/connect/api-reference/get-heartbeat.md) to check the server is up, then [List vaults](https://www.1password.dev/connect/api-reference/get-vaults.md) to see which vaults your token can read.

**3. Point it at your server and paste your token**

Select **Try it**, enter your Connect server's address, and paste your token in the **Authorization** field.

**4. Send it, or copy the code**

Select **Send** to see the live response, or copy the request as cURL, Python, JavaScript, Go, and more.

> [!NOTE]
>
> The **Try it** panel sends requests from the docs, so it can't reach a Connect server that only listens on localhost or a private network. In that case, copy the cURL request from the example panel and run it on a machine that can reach your server.

## Response object models

### APIRequest object

| Parameter | Type | Description |
| - | - | - |
| `requestID` | string | The UUID for the request. |
| `timestamp` | dateTime | Date and time of the request. |
| `action` | string | The action taken. One of: |
| `result` | string | The result of the action. One of: |
| `actor` | object | An [Actor object](#apirequest-actor-object). |
| `resource` | object | A [Resource object](#apirequest-resource-object). |

#### APIRequest: Actor object

| Parameter | Type | Description |
| - | - | - |
| `id` | string | The UUID of the Connect server that made the request. |
| `account` | string | The UUID of the 1Password account the request went to. |
| `jti` | string | The UUID of the access token used to authenticate the request. |
| `userAgent` | string | The user agent string specified in the request. |
| `ip` | string | The IP address the request originated from. |

#### APIRequest: Resource object

| Parameter | Type | Description |
| - | - | - |
| `type` | string | The resource requested. One of: |
| `vault` | object | An object containing an `id` property with the value of the UUID of the vault requested. |
| `item` | object | An object containing an `id` property with the value of the UUID of the item requested. |
| `itemVersion` | integer | The version of the item. |

### ErrorResponse object

```json theme={null}
{
	status: 401,
	message: "Invalid or missing token"
}
```

| Parameter | Type | Description |
| - | - | - |
| `status` | integer | The HTTP status code. |
| `message` | string | A message detailing the error. |

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

| Parameter | Type | Description |
| - | - | - |
| `id` | string | The UUID of the vault. |
| `name` | string | The name of the vault. |
| `description` | string | The description for the vault. |
| `attributeVersion` | integer | The version of the vault metadata. |
| `contentVersion` | integer | The version of the vault contents. |
| `items` | integer | Number of active items in the vault. |
| `type` | string | The type of vault. One of: |
| `createdAt` | dateTime | Date and time when the vault was created. |
| `updatedAt` | dateTime | Date and time when the vault or its contents were last changed. |

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

| Parameter | Type | Description |
| - | - | - |
| `id` | string | The UUID of the item. |
| `title` | string | The title of the item. |
| `vault` | object | An object containing an `id` property whose value is the UUID of the vault the item is in. |
| `category` | string | |
| `urls` | array | Array of [URL objects](#item-url-object) containing URLs for the item. |
| `favorite` | boolean | Whether the item is marked as a favorite. |
| `tags` | array | An array of strings of the tags assigned to the item. |
| `version` | integer | The version of the item. |
| `createdAt` | dateTime | Date and time when the item was created. |
| `updatedAt` | dateTime | Date and time when the vault or its contents were last changed. |
| `lastEditedBy` | string | UUID of the account that last changed the item. |

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

| Parameter | Type | Description |
| - | - | - |
| `purpose` or `type` | string | |
| `value` | string | The value to save for the field. You can specify a `generate` field instead of `value` to create a password or other random information for the value. |
| `generate` | boolean | Generate a password and save in the value for the field. By default, the password is a 32-characters long, made up of letters, numbers, and symbols. To customize the password, include a `recipe` field. |
| `recipe` | object | A [GeneratorRecipe object](#item-generatorrecipe-object). |
| `section` | object | An object containing the UUID of a section in the item. |

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

| Name | Type | Description |
| - | - | - |
| `id` | string | The UUID of the file. |
| `name` | string | The name of the file. |
| `size` | integer | The size of the file in bytes. |
| `content_path` | string | The path to download the contents of the file. |
| `content` | string | The Base64-encoded contents of the file, if `inline_files` is set to `true`. |
| `section` | object | An object containing the UUID of a section in the item. |

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

| Name | Type | Description |
| - | - | - |
| `length` | integer | The length of the password to generate. *Optional.* |
| `characterSets` | array | An array containing of the kinds of characters to include. *Optional.* Possible values: |
| `excludeCharacters` | string | A list of all characters that should be excluded from generated passwords. *Optional.* |

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

| Name | Type | Description |
| - | - | - |
| `entropy` | integer | The unpredictability of the password, measured in bits. |
| `generated` | boolean | Whether the password was generated using the password generator. |
| `strength` | string | The strength of the password. One of: |
| `history` | array | An array of strings containing the previous passwords of the item. |

#### Item: Section object

```json theme={null}
{
	"id": "95cdbc3b-7742-47ec-9056-44d6af82d562"
	"label": "Security Questions",
}
```

| Name | Type | Description |
| - | - | - |
| `id` | string | A unique identifier for the section. |
| `label` | string | The label for the section. |

#### Item: URL object

```json theme={null}
{
	"label": "website",
	"primary": true,
	"href": "https://example.com"
}
```

| Name | Type | Description |
| - | - | - |
| `label` | string | The label for the URL. |
| `primary` | boolean | Whether this is the primary URL for the item. |
| `href` | string | The address. |

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

| Name | Type | Description |
| - | - | - |
| `name` | string | Name of the server |
| `version` | string | Version info of the Connect server |
| `dependencies` | array | An array of [Service Dependencies](#server-health-dependency-object). |

#### Server Health: Dependency object

| Name | Type | Description |
| - | - | - |
| `service` | string | Name of the dependency |
| `status` | string | The service's reported status |
| `message` | string | Extra information about the dependency's status. *Optional.* |


## Related topics

- [Get all Vaults](https://www.1password.dev/connect/api-reference/get-vaults.md)
- [Use 1Password CLI with a Connect server](https://www.1password.dev/connect/cli.md)
- [1Password integrations](https://www.1password.dev/integrations.md)

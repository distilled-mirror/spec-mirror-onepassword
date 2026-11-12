> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Events API reference

> [!TIP]
>
> **Want to send a live request?** Each endpoint has its own page with a **Try it** panel: [Introspect](https://www.1password.dev/events-api/reference/introspect.md), [Audit events](https://www.1password.dev/events-api/reference/audit-events.md), [Item usages](https://www.1password.dev/events-api/reference/items-usage.md), and [Sign-in attempts](https://www.1password.dev/events-api/reference/sign-in-attempt.md).

This API reference documents the latest version of the [1Password Events API specifications (1.4.1)](https://i.1password.com/media/1password-events-reporting/1password-events-api_1.4.1.yaml). Learn more about [API versions](https://www.1password.dev/events-api/conventions.md#endpoints).

> [!NOTE]
>
> **About pagination**
>
> All POST endpoints in this API use cursor-based pagination. Your request body must include either a [`ResetCursor`](https://www.1password.dev/events-api/conventions#pagination) object (for your first request) or the `cursor` value from a previous response (for all subsequent requests).
>
> The cursor is a persistent checkpoint that marks your exact position in the event stream and remains valid across API sessions. You should store the cursor you receive in each response so you can always resume from where you left off without missing any events. Learn more about [how cursors work](https://www.1password.dev/events-api/conventions.md#pagination).

## GET /api/v2/auth/introspect

```text theme={null}
base_url/api/v2/auth/introspect
```

A GET call to this endpoint returns a list of events (features) a bearer token is authorized to access, including one or more of: audit events, item usage, and sign-in attempts. It also returns the UUID of the account where the token was issued.

### Parameters

No parameters.

### Requests

Use the full URL of the `introspect` endpoint with your [bearer token](https://www.1password.dev/events-api/authorization.md) and the required [request headers](https://www.1password.dev/events-api/conventions.md#request-headers). A GET request doesn't include a body, so the content type header isn't needed.

For example:

```shell theme={null}
curl --request GET \
  --url base_url/api/v2/auth/introspect \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN'
```

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an `Introspection` object</dd>
  <dt>400</dt>
  <dd>Bad request</dd>
  <dt>401</dt>
  <dd>Unauthorized access</dd>
  <dt>500</dt>
  <dd>Internal server error</dd>
</dl>

A successful `200` response returns an `Introspection` object with information about the token.

> [tab] Example introspection response
>
> ```json theme={null}
> {
>   "uuid": "OK41XEGLRTH4YKO5YRTCPNX3IU",
>   "issued_at": "2023-03-05T16:32:50-03:00",
>   "features": [
>     "auditevents",
>     "itemusages",
>     "signinattempts"
>   ],
> 	"account_uuid": "M4E2SWNZAZFTRGQGDNS2E5A4MU"
> }
> ```

> [tab] IntrospectionV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the Events Reporting integration. |
> | `issued_at` | string | The date and time the token was issued. Uses the [RFC 3339 standard.](https://datatracker.ietf.org/doc/html/rfc3339) |
> | `features` | array of strings | |
> | `account_uuid` | string | The UUID of the account where the bearer token was issued. |

## POST /api/v2/auditevents

```text theme={null}
base_url/api/v2/auditevents
```

A POST call to this endpoint returns information about actions performed by team members within a 1Password account. Events include when an action was performed and by whom, along with details about the type and object of the action and any other information about the activity. MSP accounts include additional information about the actor's account and type. Learn more about [audit events](https://www.1password.dev/events-api/audit-events.md).

This endpoint requires a [bearer token](https://www.1password.dev/events-api/authorization.md) with the `auditevents` feature. You can make an [introspection call](#get-%2Fapi%2Fv2%2Fauth%2Fintrospect) to the API to verify if your token is authorized to access audit events.

### Parameters

No parameters.

### Requests

Use the full URL of the `auditevents` endpoint with your [bearer token](https://www.1password.dev/events-api/authorization.md) and the required [request headers](https://www.1password.dev/events-api/conventions.md#request-headers). You must include a [ResetCursor](https://www.1password.dev/events-api/conventions.md#pagination) object or the [cursor](https://www.1password.dev/events-api/conventions.md#pagination) from a previous response in the request body.

> [tab] Example audit events request with a reset cursor
>
> ```shell theme={null}
> curl --request POST \
>   --url base_url/api/v2/auditevents \
>   --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
>   --header 'Content-Type: application/json' \
>   --data '{
>     "limit": 100,
>     "start_time": "2023-03-15T16:32:50-03:00"
> }'
> ```

> [tab] Example audit events request with a continuing cursor
>
> ```shell theme={null}
> curl --request POST \
>   --url base_url/api/v2/auditevents \
>   --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
>   --header 'Content-Type: application/json' \
>   --data '{
>     "cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK"
> }'
> ```

### Responses

A successful `200` response returns an `AuditEventItemsV2` object wrapping cursor properties and an array of `AuditEventV2` objects.

The included cursor is a persistent checkpoint that marks your current position in the event stream. Save this value and include it in the request body of your next call to this endpoint. Learn more about [how cursors work](https://www.1password.dev/events-api/conventions.md#pagination).

<dl>
  <dt>200</dt>
  <dd>Returns an `AuditEventItemsV2` object</dd>
  <dt>400</dt>
  <dd>Bad request</dd>
  <dt>401</dt>
  <dd>Unauthorized access</dd>
  <dt>500</dt>
  <dd>Internal server error</dd>
</dl>

> [tab] Example audit event response
>
> ```json theme={null}
> {
> 	"cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK",
> 	"has_more": true,
> 	"items": [
> 		{
> 			"uuid": "56YE2TYN2VFYRLNSHKPW5NVT5E",
> 			"timestamp": "2023-03-15T16:33:50-03:00",
> 			"actor_uuid": "4HCGRGYCTRQFBMGVEGTABYDU2V",
> 			"actor_details": {
> 				"uuid:": "4HCGRGYCTRQFBMGVEGTABYDU2V",
> 				"name": "Jeff Shiner",
> 				"email": "jeff_shiner@agilebits.com"
> 			},
> 			"action": "join",
> 			"object_type": "gm",
> 			"object_uuid": "pf8soyakgngrphytsyjed4ae3u",
> 			"aux_id": 9277034,
> 			"aux_uuid": "K6VFYDCJKHGGDI7QFAXX65LCDY",
> 			"aux_details": {
> 				"uuid": "K6VFYDCJKHGGDI7QFAXX65LCDY",
> 				"name": "Wendy Appleseed",
> 				"email": "wendy_appleseed@agilebits.com"
> 			},
> 			"aux_info": "R",
> 			"session": {
> 				"uuid": "A5K6COGVRVEJXJW3XQZGS7VAMM",
> 				"login_time": "2023-03-15T16:33:50-03:00",
> 				"device_uuid": "lc5fqgbrcm4plajd8mwncv2b3u",
> 				"ip": "192.0.2.254"
> 			},
> 			"location": {
> 				"country": "Canada",
> 				"region": "Ontario",
> 				"city": "Toronto",
> 				"latitude": 43.5991,
> 				"longitude": -79.4988
> 			}
> 		}
> 	]
> }
> ```

> [tab] AuditEventV2 object schemas
>
> #### AuditEventItemsV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `cursor` | string | Cursor to return more event data or to continue polling. |
> | `has_more` | boolean | Whether there's more data to be returned using the cursor. If the value is `true`, there may be more events. If the value is `false`, there are no more events at this time. Save the cursor and use it in your next polling request to to resume from where you left off and avoid missing events. |
> | `items` | array | An array of [AuditEventV2 objects](#auditeventv2-object-schema). |
>
> ##### AuditEventV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the action event. |
> | `timestamp` | string | The date and time when the action was performed. Uses the [RFC 3339 standard.](https://datatracker.ietf.org/doc/html/rfc3339) |
> | `actor_uuid` | string | The UUID of the user who performed the action. |
> | `actor_details` | object | A [user object](#userv2-object-schema). |
> | `actor_type` | string | |
> | `actor_account_uuid` | string | The UUID of the account the user belongs to. |
> | `account_uuid` | string | The UUID of the account where the action was performed. |
> | `action` | string | |
> | `object_type` | string | |
> | `object_uuid` | string | The unique identifier for the object the action was performed on. |
> | `object_details` | object | An [object details object](#object-details-object-schema). Returned if the object is a user. |
> | `aux_id` | integer | The identifier for someone or something that provides additional information about the activity. For example, the ID of a device that a user adds or removes from an account. |
> | `aux_uuid` | string | The unique identifier for someone or something that provides additional information about the activity. For example, the UUID of a team member who joins or leaves a group in an account. |
> | `aux_details` | object | An [aux details object](#aux-details-object-schema). Returned if the aux details relate to a user. |
> | `aux_info` | string | Additional information about the activity. |
> | `session` | object | A [session object](#session-object-schema). |
> | `location` | object | A [location object](#location-object-schema) that contains details about the geolocation of the client based on the client's IP address at the time the event was performed. |
>
> ###### UserV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the user who performed the action. |
> | `name` | string | The name of the user who performed the action. |
> | `email` | string | The email address of the user who performed the action. |
> | `user_type`  (MSP accounts only) | string | |
> | `user_account_uuid`  (MSP accounts only) | string | The UUID of the user's account. |
>
> ###### Object details object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the user who is the object of the action. |
> | `name` | string | The name of the user who is the object of the action. |
> | `email` | string | The email address of the user who is the object of the action. |
>
> ###### Aux details object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the user related to the additional information about the activity. For example, the user who was added to or removed from the account or vault or whom created or deleted the device. |
> | `name` | string | The name of the user related to the additional information about the activity. |
> | `email` | string | The email address of the user related to the additional information about the activity. |
>
> ###### Session object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the session. |
> | `login_time` | string | The date and time the client signed in and started the session. Uses the [RFC 3339 standard.](https://datatracker.ietf.org/doc/html/rfc3339) |
> | `device_uuid` | string | The UUID of the device signed in to the session. |
> | `ip` | string | The IP address used for the session. |
>
> ###### Location object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `country` | string | The country where the action was performed. |
> | `region` | string | The region where the action was performed. |
> | `city` | string | The city where the action was performed. |
> | `longitude` | number | A coordinate that specifies the longitudinal location for where the action was performed. |
> | `latitude` | number | A coordinate that specifies the latitudinal location for where the action was performed. |

## POST /api/v2/itemusages

```text theme={null}
base_url/api/v2/itemusages
```

A POST call to this endpoint returns information about items in shared vaults that have been modified, accessed, or used. Events include the name and IP address of the user who accessed the item, when the item was accessed, and the vault where the item is stored. Learn more about [item usage actions](https://www.1password.dev/events-api/item-usage-actions.md).

This endpoint requires a [bearer token](https://www.1password.dev/events-api/authorization.md) with the `itemusages` feature. You can make an [introspection call](#get-%2Fapi%2Fv2%2Fauth%2Fintrospect) to the API to verify if your token is authorized to access sign-in events.

### Parameters

No parameters.

### Requests

Use the full URL of the `itemusages` endpoint with your [bearer token](https://www.1password.dev/events-api/authorization.md) and the required [request headers](https://www.1password.dev/events-api/conventions.md#request-headers). You must include a [ResetCursor](https://www.1password.dev/events-api/conventions.md#pagination) object or the [cursor](https://www.1password.dev/events-api/conventions.md#pagination) from a previous response in the request body.

> [tab] Example item usage request with a reset cursor
>
> ```shell theme={null}
> curl --request POST \
>   --url base_url/api/v2/itemusages \
>   --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
>   --header 'Content-Type: application/json' \
>   --data '{
>     "limit": 100,
>     "start_time": "2023-03-15T16:32:50-03:00"
> }'
> ```

> [tab] Example item usage request with a continuing cursor
>
> ```shell theme={null}
> curl --request POST \
>   --url base_url/api/v2/itemusages \
>   --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
>   --header 'Content-Type: application/json' \
>   --data '{
>     "cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK"
> }'
> ```

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns an `ItemUsageV2` response object</dd>
  <dt>400</dt>
  <dd>Bad request</dd>
  <dt>401</dt>
  <dd>Unauthorized access</dd>
  <dt>500</dt>
  <dd>Internal server error</dd>
</dl>

A successful `200` response returns an `ItemUsageItemsV2` object wrapping cursor properties and an array of `ItemUsageV2` objects.

The included cursor is a persistent checkpoint that marks your current position in the event stream. Save this value and include it in the request body of your next call to this endpoint. Learn more about [how cursors work](https://www.1password.dev/events-api/conventions.md#pagination).

> [tab] Example item usage response
>
> ```json theme={null}
> {
> 	"cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK",
> 	"has_more": true,
> 	"items": [
> 		{
> 			"uuid": "56YE2TYN2VFYRLNSHKPW5NVT5E",
> 			"timestamp": "2023-03-15T16:33:50-03:00",
> 			"used_version": 0,
> 			"vault_uuid": "VZSYVT2LGHTBWBQGUJAIZVRABM",
> 			"item_uuid": "SDGD3I4AJYO6RMHRK8DYVNFIDZ",
> 			"user": {
> 				"uuid": "4HCGRGYCTRQFBMGVEGTABYDU2V",
> 				"name": "Wendy Appleseed",
> 				"email": "wendy_appleseed@agilebits.com"
> 			},
> 			"client": {
> 				"app_name": "1Password Browser",
> 				"app_version": "20240",
> 				"platform_name": "Chrome",
> 				"platform_version": "string",
> 				"os_name": "MacOSX",
> 				"os_version": "13.2",
> 				"ip_address": "192.0.2.254"
> 			},
> 			"location": {
> 				"country": "Canada",
> 				"region": "Ontario",
> 				"city": "Toronto",
> 				"latitude": 43.5991,
> 				"longitude": -79.4988
> 			},
> 			"action": "secure-copy"
> 		}
> 	]
> }
> ```

> [tab] ItemUsageV2 object schemas
>
> #### ItemUsageItemsV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `items` | array | An array of [ItemUsageV2 objects](#itemusagev2-object-schema). |
> | `cursor` | string | Cursor to return more event data or to continue polling. |
> | `has_more` | boolean | Whether there's more data to be returned using the cursor. If the value is `true`, there may be more events. If the value is `false`, there are no more events at this time. Save the cursor and use it in your next polling request to to resume from where you left off and avoid missing events. |
>
> ##### ItemUsageV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the event. |
> | `timestamp` | string | The date and time of the event. [RFC 3339 standard.](https://datatracker.ietf.org/doc/html/rfc3339) |
> | `used_version` | integer | The version of the item that was accessed. |
> | `vault_uuid` | string | The UUID of the vault the item is in. |
> | `item_uuid` | string | The UUID of the item that was accessed. |
> | `action` | string | |
> | `user` | object | A [user object](#userv2-object-schema-1). |
> | `client` | object | A [client object](#client-object-schema). |
> | `location` | object | A [location object](#location-object-schema-1) that contains details about the geolocation of the client based on the client's IP address at the time the event was performed. |
> | `account_uuid`  (MSP accounts only) | string | The UUID of the account where the action was performed. |
>
> ###### UserV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the user that accessed the item or attempted to sign in to the account. |
> | `name` | string | The name of the user, hydrated at the time the event was generated. |
> | `email` | string | The email address of the user, hydrated at the time the event was generated. |
> | `user_type`  (MSP accounts only) | string | |
> | `user_account_uuid`  (MSP accounts only) | string | The UUID of the user's account. |
>
> ###### Client object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `app_name` | string | The name of the 1Password app the item was accessed from. |
> | `app_version` | string | The version number of the app. |
> | `platform_name` | string | The name of the platform the item was accessed from. |
> | `platform_version` | string | The version of the browser or computer where 1Password is installed or the CPU of the machine where the 1Password command-line tool is installed. |
> | `os_name` | string | The name of the operating system the item was accessed from. |
> | `os_version` | string | The version of the operating system the item was accessed from. |
> | `ip_address` | string | The IP address the item was accessed from. |
>
> ###### Location object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `country` | string | The country where the item was accessed. |
> | `region` | string | The region where the item was accessed. |
> | `city` | string | The city where the item was accessed. |
> | `longitude` | number | A coordinate that specifies the longitudinal location for where the item was accessed. |
> | `latitude` | number | A coordinate that specifies the latitudinal location for where the item was accessed. |

## POST /api/v2/signinattempts

```text theme={null}
base_url/api/v2/signinattempts
```

A POST call to this endpoint returns information about sign-in attempts. Events include the name and IP address of the user who attempted to sign in to the account, when the attempt was made, and – for failed attempts – the cause of the failure. For MSP accounts, events also include additional information about the user's account and type.

This endpoint requires a [bearer token](https://www.1password.dev/events-api/authorization.md) with the `signinattempts` feature. You can make an [introspection call](#get-%2Fapi%2Fv2%2Fauth%2Fintrospect) to the API to verify if your token is authorized to access sign-in events.

### Parameters

No parameters.

### Requests

Use the full URL of the `signinattempts` endpoint with your [bearer token](https://www.1password.dev/events-api/authorization.md) and the required [request headers](https://www.1password.dev/events-api/conventions.md#request-headers). You must include a [ResetCursor](https://www.1password.dev/events-api/conventions.md#pagination) object or the [cursor](https://www.1password.dev/events-api/conventions.md#pagination) from a previous response in the request body.

> [tab] Example request with a reset cursor
>
> ```shell theme={null}
> curl --request POST \
>   --url base_url/api/v2/signinattempts \
>   --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
>   --header 'Content-Type: application/json' \
>   --data '{
>     "limit": 100,
>     "start_time": "2023-03-15T16:32:50-03:00"
> }'
> ```

> [tab] Example request with a continuing cursor
>
> ```shell theme={null}
> curl --request POST \
>   --url base_url/api/v2/signinattempts \
>   --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
>   --header 'Content-Type: application/json' \
>   --data '{
>     "cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK"
> }'
> ```

### Responses

<dl>
  <dt>200</dt>
  <dd>Returns a `SignInAttemptItemsV2` object</dd>
  <dt>400</dt>
  <dd>Bad request</dd>
  <dt>401</dt>
  <dd>Unauthorized access</dd>
  <dt>500</dt>
  <dd>Internal server error</dd>
</dl>

A successful `200` response returns a `SignInAttemptItemsV2` object wrapping cursor properties and an array of `SignInAttemptV2` objects.

The included cursor is a persistent checkpoint that marks your current position in the event stream. Save this value and include it in the request body of your next call to this endpoint. Learn more about [how cursors work](https://www.1password.dev/events-api/conventions.md#pagination).

> [tab] Example sign-in attempt response
>
> ```json theme={null}
> {
> 	"cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK",
> 	"has_more": true,
> 	"items": [
> 		{
> 			"uuid": "56YE2TYN2VFYRLNSHKPW5NVT5E",
> 			"session_uuid": "A5K6COGVRVEJXJW3XQZGS7VAMM",
> 			"timestamp": "2023-03-15T16:32:50-03:00",
> 			"category": "firewall_failed",
> 			"type": "continent_blocked",
> 			"country": "France",
> 			"details": {
> 				"value": "Europe"
> 			},
> 			"target_user": {
> 				"uuid": "IR7VJHJ36JHINBFAD7V2T5MP3E",
> 				"name": "Wendy Appleseed",
> 				"email": "wendy_appleseed@agilebits.com"
> 			},
> 			"client": {
> 				"app_name": "1Password Browser",
> 				"app_version": "20240",
> 				"platform_name": "Chrome",
> 				"platform_version": "string",
> 				"os_name": "MacOSX",
> 				"os_version": "13.2",
> 				"ip_address": "192.0.2.254"
> 			},
> 			"location": {
> 				"country": "Canada",
> 				"region": "Ontario",
> 				"city": "Toronto",
> 				"latitude": 43.5991,
> 				"longitude": -79.4988
> 			}
> 		}
> 	]
> }
> ```

> [tab] SignInAttemptsV2 object schemas
>
> #### SignInAttemptItemsV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `items` | array | An array of [SignInAttemptsV2 objects](#signinattemptsv2-object-schema). |
> | `cursor` | string | Cursor to return more event data or to continue polling. |
> | `has_more` | boolean | Whether there's more data to be returned using the cursor. If the value is `true`, there may be more events. If the value is `false`, there are no more events at this time. Save the cursor and use it in your next polling request to to resume from where you left off and avoid missing events. |
>
> ##### SignInAttemptsV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the event. |
> | `session_uuid` | string | The UUID of the session that created the event. |
> | `timestamp` | string | The date and time of the sign-in attempt. Uses the [RFC 3339 standard.](https://datatracker.ietf.org/doc/html/rfc3339) |
> | `category` | string | |
> | | `type` | string |
> | `country` | string | The country code of the event. Uses the [ ISO 3166 standard.](https://www.iso.org/iso-3166-country-codes.html) |
> | `details` | object | A [details object](#details-object-schema) that contains additional information about the sign-in attempt. |
> | `target_user` | object | A [user object](#userv2-object-schema-2). |
> | `client` | object | A [client object](#client-object-schema-1). |
> | `location` | object | A [location object](#location-object-schema-2) that contains details about the geolocation of the client based on the client's IP address at the time the event was performed. |
> | `account_uuid`  (MSP accounts only) | string | The UUID of the account where the action was performed. |
>
> ###### Details object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `value` | string | The additional information about the sign-in attempt, such as any firewall rules that prevent a user from signing in. For example, in the event of a sign-in attempt blocked by firewall rules, the value is the country, continent, or IP address of the sign-in attempt. |
>
> ###### UserV2 object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `uuid` | string | The UUID of the user that accessed the item or attempted to sign in to the account. |
> | `name` | string | The name of the user, hydrated at the time the event was generated. |
> | `email` | string | The email address of the user, hydrated at the time the event was generated. |
> | `user_type`  (MSP accounts only) | string | |
> | `user_account_uuid`  (MSP accounts only) | string | The UUID of the user's account. |
>
> ###### Client object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `app_name` | string | The name of the 1Password app the item was accessed from. |
> | `app_version` | string | The version number of the app. |
> | `platform_name` | string | The name of the platform the item was accessed from. |
> | `platform_version` | string | The version of the browser or computer where 1Password is installed or the CPU of the machine where the 1Password command-line tool is installed. |
> | `os_name` | string | The name of the operating system the item was accessed from. |
> | `os_version` | string | The version of the operating system the item was accessed from. |
> | `ip_address` | string | The IP address the item was accessed from. |
>
> ###### Location object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `country` | string | The country where the sign-in attempt was made. |
> | `region` | string | The region where the sign-in attempt was made. |
> | `city` | string | The city where the sign-in attempt was made. |
> | `longitude` | number | A coordinate that specifies the longitudinal location where the sign-in attempt was made. |
> | `latitude` | number | A coordinate that specifies the latitudinal location where the sign-in attempt was made. |

## ErrorResponse object

> [tab] Example error response
>
> ```json theme={null}
> {
> 	status: 401,
> 	message: "Unauthorized access"
> }
> ```

> [tab] ErrorResponse object schema
>
> | Name | Type | Description |
> | - | - | - |
> | `status` | integer | The HTTP status code. |
> | `message` | string | A message detailing the error. |


## Related topics

- [Introspect bearer token (v2)](https://www.1password.dev/events-api/reference/introspect.md)
- [Events API conventions](https://www.1password.dev/events-api/conventions.md)
- [Item usage actions](https://www.1password.dev/events-api/item-usage-actions.md)

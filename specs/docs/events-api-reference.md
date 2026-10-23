> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Events API reference

This API reference documents the latest version of the [1Password Events API specifications (1.4.1)](https://i.1password.com/media/1password-events-reporting/1password-events-api_1.4.1.yaml). Learn more about [API versions](/events-api/endpoints.md#endpoint-versions).

> [!NOTE]
>
> **About pagination**
>
> All POST endpoints in this API use cursor-based pagination. Your request body must include either a [`ResetCursor`](/events-api/pagination#reset-cursor) object (for your first request) or the `cursor` value from a previous response (for all subsequent requests).
>
> The cursor is a persistent checkpoint that marks your exact position in the event stream and remains valid across API sessions. You should store the cursor you receive in each response so you can always resume from where you left off without missing any events. Learn more about [how cursors work](/events-api/pagination.md).

## GET /api/v2/auth/introspect

```
base_url/api/v2/auth/introspect
```

A GET call to this endpoint returns a list of events (features) a bearer token is authorized to access, including one or more of: audit events, item usage, and sign-in attempts. It also returns the UUID of the account where the token was issued.

### Parameters

No parameters.

### Requests

Use the full URL of the `introspect` endpoint with your [bearer token](/events-api/authorization.md) and the required [request headers](/events-api/request-headers.md). A GET request doesn't include a body, so the content type header isn't needed.

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

**Example introspection response**

```json theme={null}
{
  "uuid": "OK41XEGLRTH4YKO5YRTCPNX3IU",
  "issued_at": "2023-03-05T16:32:50-03:00",
  "features": [
    "auditevents",
    "itemusages",
    "signinattempts"
  ],
	"account_uuid": "M4E2SWNZAZFTRGQGDNS2E5A4MU"
}
```

**IntrospectionV2 object schema**

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the Events Reporting integration.</td>
    </tr>

    <tr>
      <td>`issued_at`</td>
      <td>string</td>
      <td>The date and time the token was issued. Uses the [RFC 3339 standard](https://datatracker.ietf.org/doc/html/rfc3339).</td>
    </tr>

    <tr>
      <td>`features`</td>
      <td>array of strings</td>

      <td>
        A list of event features the integration has access to. Possible values are one or more of:

        <ul>
          <li>`auditevents`</li>
          <li>`itemusages`</li>
          <li>`signinattempts`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td>`account_uuid`</td>
      <td>string</td>
      <td>The UUID of the account where the bearer token was issued.</td>
    </tr>
  </tbody>
</table>

## POST /api/v2/auditevents

```
base_url/api/v2/auditevents
```

A POST call to this endpoint returns information about actions performed by team members within a 1Password account. Events include when an action was performed and by whom, along with details about the type and object of the action and any other information about the activity. MSP accounts include additional information about the actor's account and type. Learn more about [audit events](/events-api/audit-events.md).

This endpoint requires a [bearer token](/events-api/authorization.md) with the `auditevents` feature. You can make an [introspection call](#get-%2Fapi%2Fv2%2Fauth%2Fintrospect) to the API to verify if your token is authorized to access audit events.

### Parameters

No parameters.

### Requests

Use the full URL of the `auditevents` endpoint with your [bearer token](/events-api/authorization.md) and the required [request headers](/events-api/request-headers.md). You must include a [ResetCursor](/events-api/pagination.md#reset-cursor) object or the [cursor](/events-api/pagination.md#cursor) from a previous response in the request body.

**Example audit events request with a reset cursor**

```shell theme={null}
curl --request POST \
  --url base_url/api/v2/auditevents \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
  --header 'Content-Type: application/json' \
  --data '{
    "limit": 100,
    "start_time": "2023-03-15T16:32:50-03:00"
}'
```

**Example audit events request with a continuing cursor**

```shell theme={null}
curl --request POST \
  --url base_url/api/v2/auditevents \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
  --header 'Content-Type: application/json' \
  --data '{
    "cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK"
}'
```

### Responses

A successful `200` response returns an `AuditEventItemsV2` object wrapping cursor properties and an array of `AuditEventV2` objects.

The included cursor is a persistent checkpoint that marks your current position in the event stream. Save this value and include it in the request body of your next call to this endpoint. Learn more about [how cursors work](/events-api/pagination.md).

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

**Example audit event response**

```json theme={null}
{
	"cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK",
	"has_more": true,
	"items": [
		{
			"uuid": "56YE2TYN2VFYRLNSHKPW5NVT5E",
			"timestamp": "2023-03-15T16:33:50-03:00",
			"actor_uuid": "4HCGRGYCTRQFBMGVEGTABYDU2V",
			"actor_details": {
				"uuid:": "4HCGRGYCTRQFBMGVEGTABYDU2V",
				"name": "Jeff Shiner",
				"email": "jeff_shiner@agilebits.com"
			},
			"action": "join",
			"object_type": "gm",
			"object_uuid": "pf8soyakgngrphytsyjed4ae3u",
			"aux_id": 9277034,
			"aux_uuid": "K6VFYDCJKHGGDI7QFAXX65LCDY",
			"aux_details": {
				"uuid": "K6VFYDCJKHGGDI7QFAXX65LCDY",
				"name": "Wendy Appleseed",
				"email": "wendy_appleseed@agilebits.com"
			},
			"aux_info": "R",
			"session": {
				"uuid": "A5K6COGVRVEJXJW3XQZGS7VAMM",
				"login_time": "2023-03-15T16:33:50-03:00",
				"device_uuid": "lc5fqgbrcm4plajd8mwncv2b3u",
				"ip": "192.0.2.254"
			},
			"location": {
				"country": "Canada",
				"region": "Ontario",
				"city": "Toronto",
				"latitude": 43.5991,
				"longitude": -79.4988
			}
		}
	]
}
```

**AuditEventV2 object schemas**

#### AuditEventItemsV2 object schema

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
      <td>`cursor`</td>
      <td>string</td>
      <td>Cursor to return more event data or to continue polling.</td>
    </tr>

    <tr>
      <td>`has_more`</td>
      <td>boolean</td>
      <td>Whether there's more data to be returned using the cursor. If the value is `true`, there may be more events. If the value is `false`, there are no more events at this time. Save the cursor and use it in your next polling request to to resume from where you left off and avoid missing events.</td>
    </tr>

    <tr>
      <td>`items`</td>
      <td>array</td>
      <td>An array of [AuditEventV2 objects](#auditeventv2-object-schema).</td>
    </tr>
  </tbody>
</table>

##### AuditEventV2 object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the action event.</td>
    </tr>

    <tr>
      <td>`timestamp`</td>
      <td>string</td>
      <td>The date and time when the action was performed. Uses the [RFC 3339 standard](https://datatracker.ietf.org/doc/html/rfc3339).</td>
    </tr>

    <tr>
      <td>`actor_uuid`</td>
      <td>string</td>
      <td>The UUID of the user who performed the action.</td>
    </tr>

    <tr>
      <td>`actor_details`</td>
      <td>object</td>
      <td>A [user object](#userv2-object-schema).</td>
    </tr>

    <tr>
      <td valign="top">`actor_type`</td>
      <td valign="top">string</td>

      <td>
        The type of user who performed the action (internal or external). Possible values are:

        <ul>
          <li>`user`</li>
          <li>`external_user` (MSP accounts only)</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td>`actor_account_uuid`</td>
      <td>string</td>
      <td>The UUID of the account the user belongs to.</td>
    </tr>

    <tr>
      <td>`account_uuid`</td>
      <td>string</td>
      <td>The UUID of the account where the action was performed.</td>
    </tr>

    <tr>
      <td valign="top">`action`</td>
      <td valign="top">string</td>

      <td>
        The type of action that was performed. Possible values are:

        <ul>
          <li>`"activate"`</li>
          <li>`"addgsso"`</li>
          <li>`"begin"`</li>
          <li>`"beginr"`</li>
          <li>`"cancel"`</li>
          <li>`"cancelr"`</li>
          <li>`"changeks"`</li>
          <li>`"changeks"`</li>
          <li>`"changela"`</li>
          <li>`"changemp"`</li>
          <li>`"changenm"`</li>
          <li>`"changesk"`</li>
          <li>`"chngasso"`</li>
          <li>`"chngdsso"`</li>
          <li>`"chngpsso"`</li>
          <li>`"complete"`</li>
          <li>`"completr"`</li>
          <li>`"convert"`</li>
          <li>`"create"`</li>
          <li>`"dealldev"`</li>
          <li>`"delete"`</li>
          <li>`"delgsso"`</li>
          <li>`"delshare"`</li>
          <li>`"deolddev"`</li>
          <li>`"detchild"`</li>
          <li>`"disblduo"`</li>
          <li>`"disblmfa"`</li>
          <li>`"disblsso"`</li>
          <li>`"dlgsess"`</li>
          <li>`"dvrfydmn"`</li>
          <li>`"enblduo"`</li>
          <li>`"enblmfa"`</li>
          <li>`"enblsso"`</li>
          <li>`"expire"`</li>
          <li>`"export"`</li>
          <li>`"grant"`</li>
          <li>`"hide"`</li>
          <li>`"join"`</li>
          <li>`"launchi"`</li>
          <li>`"leave"`</li>
          <li>`"musercom"`</li>
          <li>`"muserdec"`</li>
          <li>`"patch"`</li>
          <li>`"propose"`</li>
          <li>`"provsn"`</li>
          <li>`"prsndall"`</li>
          <li>`"purge"`</li>
          <li>`"rdmchild"`</li>
          <li>`"reactive"`</li>
          <li>`"reauth"`</li>
          <li>`"replace"`</li>
          <li>`"replace"`</li>
          <li>`"resendts"`</li>
          <li>`"revoke"`</li>
          <li>`"role"`</li>
          <li>`"sdvcsso"`</li>
          <li>`"sendpkg"`</li>
          <li>`"sendts"`</li>
          <li>`"share"`</li>
          <li>`"ssotknv"`</li>
          <li>`"suspend"`</li>
          <li>`"tdvcsso"`</li>
          <li>`"trename"`</li>
          <li>`"trevoke"`</li>
          <li>`"trvlaway"`</li>
          <li>`"trvlback"`</li>
          <li>`"tverify"`</li>
          <li>`"uisas"`</li>
          <li>`"unhide"`</li>
          <li>`"unknown"`</li>
          <li>`"unlink"`</li>
          <li>`"updatduo"`</li>
          <li>`"update"`</li>
          <li>`"updatea"`</li>
          <li>`"updatfw"`</li>
          <li>`"updatmfa"`</li>
          <li>`"upguest"`</li>
          <li>`"uvrfydmn"`</li>
          <li>`"verify"`</li>
          <li>`"view"`</li>
          <li>`"vrfydmn"`</li>
        </ul>

        [Learn about audit event actions](/events-api/audit-events.md).
      </td>
    </tr>

    <tr>
      <td valign="top">`object_type`</td>
      <td valign="top">string</td>

      <td>
        The type of object the action was performed on. Possible values are:

        <ul>
          <li>`"account"`</li>
          <li>`"card"`</li>
          <li>`"cred"`</li>
          <li>`"device"`</li>
          <li>`"dlgdsess"`</li>
          <li>`"ec"`</li>
          <li>`"famchild"`</li>
          <li>`"file"`</li>
          <li>`"gm"`</li>
          <li>`"group"`</li>
          <li>`"gva"`</li>
          <li>`"invite"`</li>
          <li>`"item"`</li>
          <li>`"itemhist"`</li>
          <li>`"items"`</li>
          <li>`"miguser"`</li>
          <li>`"mngdacc"`</li>
          <li>`"pm"`</li>
          <li>`"report"`</li>
          <li>`"sa"`</li>
          <li>`"satoken"`</li>
          <li>`"slackapp"`</li>
          <li>`"sso"`</li>
          <li>`"ssotkn"`</li>
          <li>`"sub"`</li>
          <li>`"template"`</li>
          <li>`"user"`</li>
          <li>`"uva"`</li>
          <li>`"vault"`</li>
          <li>`"vaultkey"`</li>
        </ul>

        [Learn about audit event objects](/events-api/audit-events.md).
      </td>
    </tr>

    <tr>
      <td>`object_uuid`</td>
      <td>string</td>
      <td>The unique identifier for the object the action was performed on.</td>
    </tr>

    <tr>
      <td>`object_details`</td>
      <td>object</td>
      <td>An [object details object](#object-details-object-schema). Returned if the object is a user.</td>
    </tr>

    <tr>
      <td>`aux_id`</td>
      <td>integer</td>
      <td>The identifier for someone or something that provides additional information about the activity. For example, the ID of a device that a user adds or removes from an account.</td>
    </tr>

    <tr>
      <td>`aux_uuid`</td>
      <td>string</td>
      <td>The unique identifier for someone or something that provides additional information about the activity. For example, the UUID of a team member who joins or leaves a group in an account.</td>
    </tr>

    <tr>
      <td>`aux_details`</td>
      <td>object</td>
      <td>An [aux details object](#aux-details-object-schema). Returned if the aux details relate to a user.</td>
    </tr>

    <tr>
      <td>`aux_info`</td>
      <td>string</td>
      <td>Additional information about the activity.</td>
    </tr>

    <tr>
      <td>`session`</td>
      <td>object</td>
      <td>A [session object](#session-object-schema).</td>
    </tr>

    <tr>
      <td>`location`</td>
      <td>object</td>
      <td>A [location object](#location-object-schema) that contains details about the geolocation of the client based on the client's IP address at the time the event was performed.</td>
    </tr>
  </tbody>
</table>

###### UserV2 object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the user who performed the action.</td>
    </tr>

    <tr>
      <td>`name`</td>
      <td>string</td>
      <td>The name of the user who performed the action.</td>
    </tr>

    <tr>
      <td>`email`</td>
      <td>string</td>
      <td>The email address of the user who performed the action.</td>
    </tr>

    <tr>
      <td valign="top">`user_type`\
      (MSP accounts only)</td>
      <td valign="top">string</td>

      <td>
        The type of user who performed the action (internal or external). Possible values are:

        <ul>
          <li>`user`</li>
          <li>`external_user`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td>`user_account_uuid`\
      (MSP accounts only)</td>
      <td>string</td>
      <td>The UUID of the user's account.</td>
    </tr>
  </tbody>
</table>

###### Object details object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the user who is the object of the action.</td>
    </tr>

    <tr>
      <td>`name`</td>
      <td>string</td>
      <td>The name of the user who is the object of the action.</td>
    </tr>

    <tr>
      <td>`email`</td>
      <td>string</td>
      <td>The email address of the user who is the object of the action.</td>
    </tr>
  </tbody>
</table>

###### Aux details object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the user related to the additional information about the activity. For example, the user who was added to or removed from the account or vault or whom created or deleted the device.</td>
    </tr>

    <tr>
      <td>`name`</td>
      <td>string</td>
      <td>The name of the user related to the additional information about the activity.</td>
    </tr>

    <tr>
      <td>`email`</td>
      <td>string</td>
      <td>The email address of the user related to the additional information about the activity.</td>
    </tr>
  </tbody>
</table>

###### Session object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the session.</td>
    </tr>

    <tr>
      <td>`login_time`</td>
      <td>string</td>
      <td>The date and time the client signed in and started the session. Uses the [RFC 3339 standard](https://datatracker.ietf.org/doc/html/rfc3339).</td>
    </tr>

    <tr>
      <td>`device_uuid`</td>
      <td>string</td>
      <td>The UUID of the device signed in to the session.</td>
    </tr>

    <tr>
      <td>`ip`</td>
      <td>string</td>
      <td>The IP address used for the session.</td>
    </tr>
  </tbody>
</table>

###### Location object schema

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
      <td>`country`</td>
      <td>string</td>
      <td>The country where the action was performed.</td>
    </tr>

    <tr>
      <td>`region`</td>
      <td>string</td>
      <td>The region where the action was performed.</td>
    </tr>

    <tr>
      <td>`city`</td>
      <td>string</td>
      <td>The city where the action was performed.</td>
    </tr>

    <tr>
      <td>`longitude`</td>
      <td>number</td>
      <td>A coordinate that specifies the longitudinal location for where the action was performed.</td>
    </tr>

    <tr>
      <td>`latitude`</td>
      <td>number</td>
      <td>A coordinate that specifies the latitudinal location for where the action was performed.</td>
    </tr>
  </tbody>
</table>

## POST /api/v2/itemusages

```
base_url/api/v2/itemusages
```

A POST call to this endpoint returns information about items in shared vaults that have been modified, accessed, or used. Events include the name and IP address of the user who accessed the item, when the item was accessed, and the vault where the item is stored. Learn more about [item usage actions](/events-api/item-usage-actions.md).

This endpoint requires a [bearer token](/events-api/authorization.md) with the `itemusages` feature. You can make an [introspection call](#get-%2Fapi%2Fv2%2Fauth%2Fintrospect) to the API to verify if your token is authorized to access sign-in events.

### Parameters

No parameters.

### Requests

Use the full URL of the `itemusages` endpoint with your [bearer token](/events-api/authorization.md) and the required [request headers](/events-api/request-headers.md). You must include a [ResetCursor](/events-api/pagination.md#reset-cursor) object or the [cursor](/events-api/pagination.md#cursor) from a previous response in the request body.

**Example item usage request with a reset cursor**

```shell theme={null}
curl --request POST \
  --url base_url/api/v2/itemusages \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
  --header 'Content-Type: application/json' \
  --data '{
    "limit": 100,
    "start_time": "2023-03-15T16:32:50-03:00"
}'
```

**Example item usage request with a continuing cursor**

```shell theme={null}
curl --request POST \
  --url base_url/api/v2/itemusages \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
  --header 'Content-Type: application/json' \
  --data '{
    "cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK"
}'
```

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

The included cursor is a persistent checkpoint that marks your current position in the event stream. Save this value and include it in the request body of your next call to this endpoint. Learn more about [how cursors work](/events-api/pagination.md).

**Example item usage response**

```json theme={null}
{
	"cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK",
	"has_more": true,
	"items": [
		{
			"uuid": "56YE2TYN2VFYRLNSHKPW5NVT5E",
			"timestamp": "2023-03-15T16:33:50-03:00",
			"used_version": 0,
			"vault_uuid": "VZSYVT2LGHTBWBQGUJAIZVRABM",
			"item_uuid": "SDGD3I4AJYO6RMHRK8DYVNFIDZ",
			"user": {
				"uuid": "4HCGRGYCTRQFBMGVEGTABYDU2V",
				"name": "Wendy Appleseed",
				"email": "wendy_appleseed@agilebits.com"
			},
			"client": {
				"app_name": "1Password Browser",
				"app_version": "20240",
				"platform_name": "Chrome",
				"platform_version": "string",
				"os_name": "MacOSX",
				"os_version": "13.2",
				"ip_address": "192.0.2.254"
			},
			"location": {
				"country": "Canada",
				"region": "Ontario",
				"city": "Toronto",
				"latitude": 43.5991,
				"longitude": -79.4988
			},
			"action": "secure-copy"
		}
	]
}
```

**ItemUsageV2 object schemas**

#### ItemUsageItemsV2 object schema

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
      <td>`items`</td>
      <td>array</td>
      <td>An array of [ItemUsageV2 objects](#itemusagev2-object-schema).</td>
    </tr>

    <tr>
      <td>`cursor`</td>
      <td>string</td>
      <td>Cursor to return more event data or to continue polling.</td>
    </tr>

    <tr>
      <td>`has_more`</td>
      <td>boolean</td>
      <td>Whether there's more data to be returned using the cursor. If the value is `true`, there may be more events. If the value is `false`, there are no more events at this time. Save the cursor and use it in your next polling request to to resume from where you left off and avoid missing events.</td>
    </tr>
  </tbody>
</table>

##### ItemUsageV2 object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the event.</td>
    </tr>

    <tr>
      <td>`timestamp`</td>
      <td>string</td>
      <td>The date and time of the event. [RFC 3339 standard](https://datatracker.ietf.org/doc/html/rfc3339).</td>
    </tr>

    <tr>
      <td>`used_version`</td>
      <td>integer</td>
      <td>The version of the item that was accessed.</td>
    </tr>

    <tr>
      <td>`vault_uuid`</td>
      <td>string</td>
      <td>The UUID of the vault the item is in.</td>
    </tr>

    <tr>
      <td>`item_uuid`</td>
      <td>string</td>
      <td>The UUID of the item that was accessed.</td>
    </tr>

    <tr>
      <td valign="top">`action`</td>
      <td valign="top">string</td>

      <td>
        Details about how the item was used. Actions are only captured from client apps using 1Password 8.4.0 or later. Possible values are:

        <ul>
          <li>`"enter-item-edit-mode"`</li>
          <li>`"export"`</li>
          <li>`"fill"`</li>
          <li>`"other"`</li>
          <li>`"reveal"`</li>
          <li>`"secure-copy"`</li>
          <li>`"select-sso-provider"`</li>
          <li>`"server-create"`</li>
          <li>`"server-fetch"`</li>
          <li>`"server-update"`</li>
          <li>`"share"`</li>
        </ul>

        [Learn about item usage actions](/events-api/item-usage-actions.md).
      </td>
    </tr>

    <tr>
      <td>`user`</td>
      <td>object</td>
      <td>A [user object](#userv2-object-schema-1).</td>
    </tr>

    <tr>
      <td>`client`</td>
      <td>object</td>
      <td>A [client object](#client-object-schema).</td>
    </tr>

    <tr>
      <td>`location`</td>
      <td>object</td>
      <td>A [location object](#location-object-schema-1) that contains details about the geolocation of the client based on the client's IP address at the time the event was performed.</td>
    </tr>

    <tr>
      <td>`account_uuid`\
      (MSP accounts only)</td>
      <td>string</td>
      <td>The UUID of the account where the action was performed.</td>
    </tr>
  </tbody>
</table>

###### UserV2 object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the user that accessed the item or attempted to sign in to the account.</td>
    </tr>

    <tr>
      <td>`name`</td>
      <td>string</td>
      <td>The name of the user, hydrated at the time the event was generated.</td>
    </tr>

    <tr>
      <td>`email`</td>
      <td>string</td>
      <td>The email address of the user, hydrated at the time the event was generated.</td>
    </tr>

    <tr>
      <td valign="top">`user_type`\
      (MSP accounts only)</td>
      <td valign="top">string</td>

      <td>
        The type of user who performed the action (internal or external). Possible values are:

        <ul>
          <li>`user`</li>
          <li>`external_user`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td>`user_account_uuid`\
      (MSP accounts only)</td>
      <td>string</td>
      <td>The UUID of the user's account.</td>
    </tr>
  </tbody>
</table>

###### Client object schema

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
      <td>`app_name`</td>
      <td>string</td>
      <td>The name of the 1Password app the item was accessed from.</td>
    </tr>

    <tr>
      <td>`app_version`</td>
      <td>string</td>
      <td>The version number of the app.</td>
    </tr>

    <tr>
      <td>`platform_name`</td>
      <td>string</td>
      <td>The name of the platform the item was accessed from.</td>
    </tr>

    <tr>
      <td>`platform_version`</td>
      <td>string</td>
      <td>The version of the browser or computer where 1Password is installed or the CPU of the machine where the 1Password command-line tool is installed.</td>
    </tr>

    <tr>
      <td>`os_name`</td>
      <td>string</td>
      <td>The name of the operating system the item was accessed from.</td>
    </tr>

    <tr>
      <td>`os_version`</td>
      <td>string</td>
      <td>The version of the operating system the item was accessed from.</td>
    </tr>

    <tr>
      <td>`ip_address`</td>
      <td>string</td>
      <td>The IP address the item was accessed from.</td>
    </tr>
  </tbody>
</table>

###### Location object schema

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
      <td>`country`</td>
      <td>string</td>
      <td>The country where the item was accessed.</td>
    </tr>

    <tr>
      <td>`region`</td>
      <td>string</td>
      <td>The region where the item was accessed.</td>
    </tr>

    <tr>
      <td>`city`</td>
      <td>string</td>
      <td>The city where the item was accessed.</td>
    </tr>

    <tr>
      <td>`longitude`</td>
      <td>number</td>
      <td>A coordinate that specifies the longitudinal location for where the item was accessed.</td>
    </tr>

    <tr>
      <td>`latitude`</td>
      <td>number</td>
      <td>A coordinate that specifies the latitudinal location for where the item was accessed.</td>
    </tr>
  </tbody>
</table>

## POST /api/v2/signinattempts

```
base_url/api/v2/signinattempts
```

A POST call to this endpoint returns information about sign-in attempts. Events include the name and IP address of the user who attempted to sign in to the account, when the attempt was made, and – for failed attempts – the cause of the failure. For MSP accounts, events also include additional information about the user's account and type.

This endpoint requires a [bearer token](/events-api/authorization.md) with the `signinattempts` feature. You can make an [introspection call](#get-%2Fapi%2Fv2%2Fauth%2Fintrospect) to the API to verify if your token is authorized to access sign-in events.

### Parameters

No parameters.

### Requests

Use the full URL of the `signinattempts` endpoint with your [bearer token](/events-api/authorization.md) and the required [request headers](/events-api/request-headers.md). You must include a [ResetCursor](/events-api/pagination.md#reset-cursor) object or the [cursor](/events-api/pagination.md#cursor) from a previous response in the request body.

**Example request with a reset cursor**

```shell theme={null}
curl --request POST \
  --url base_url/api/v2/signinattempts \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
  --header 'Content-Type: application/json' \
  --data '{
    "limit": 100,
    "start_time": "2023-03-15T16:32:50-03:00"
}'
```

**Example request with a continuing cursor**

```shell theme={null}
curl --request POST \
  --url base_url/api/v2/signinattempts \
  --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
  --header 'Content-Type: application/json' \
  --data '{
    "cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK"
}'
```

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

The included cursor is a persistent checkpoint that marks your current position in the event stream. Save this value and include it in the request body of your next call to this endpoint. Learn more about [how cursors work](/events-api/pagination.md).

**Example sign-in attempt response**

```json theme={null}
{
	"cursor": "aGVsbG8hIGlzIGl0IG1lIHlvdSBhcmUgbG9va2luZyBmb3IK",
	"has_more": true,
	"items": [
		{
			"uuid": "56YE2TYN2VFYRLNSHKPW5NVT5E",
			"session_uuid": "A5K6COGVRVEJXJW3XQZGS7VAMM",
			"timestamp": "2023-03-15T16:32:50-03:00",
			"category": "firewall_failed",
			"type": "continent_blocked",
			"country": "France",
			"details": {
				"value": "Europe"
			},
			"target_user": {
				"uuid": "IR7VJHJ36JHINBFAD7V2T5MP3E",
				"name": "Wendy Appleseed",
				"email": "wendy_appleseed@agilebits.com"
			},
			"client": {
				"app_name": "1Password Browser",
				"app_version": "20240",
				"platform_name": "Chrome",
				"platform_version": "string",
				"os_name": "MacOSX",
				"os_version": "13.2",
				"ip_address": "192.0.2.254"
			},
			"location": {
				"country": "Canada",
				"region": "Ontario",
				"city": "Toronto",
				"latitude": 43.5991,
				"longitude": -79.4988
			}
		}
	]
}
```

**SignInAttemptsV2 object schemas**

#### SignInAttemptItemsV2 object schema

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
      <td>`items`</td>
      <td>array</td>
      <td>An array of [SignInAttemptsV2 objects](#signinattemptsv2-object-schema).</td>
    </tr>

    <tr>
      <td>`cursor`</td>
      <td>string</td>
      <td>Cursor to return more event data or to continue polling.</td>
    </tr>

    <tr>
      <td>`has_more`</td>
      <td>boolean</td>
      <td>Whether there's more data to be returned using the cursor. If the value is `true`, there may be more events. If the value is `false`, there are no more events at this time. Save the cursor and use it in your next polling request to to resume from where you left off and avoid missing events.</td>
    </tr>
  </tbody>
</table>

##### SignInAttemptsV2 object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the event.</td>
    </tr>

    <tr>
      <td>`session_uuid`</td>
      <td>string</td>
      <td>The UUID of the session that created the event.</td>
    </tr>

    <tr>
      <td>`timestamp`</td>
      <td>string</td>
      <td>The date and time of the sign-in attempt. Uses the [RFC 3339 standard](https://datatracker.ietf.org/doc/html/rfc3339).</td>
    </tr>

    <tr>
      <td valign="top">`category`</td>
      <td valign="top">string</td>

      <td>
        The category of the sign-in attempt. Possible values are:

        <ul>
          <li>`"success"`</li>
          <li>`"credentials_failed"`</li>
          <li>`"mfa_failed"`</li>
          <li>`"sso_failed"`</li>
          <li>`"modern_version_failed"`</li>
          <li>`"firewall_failed"`</li>
          <li>`"firewall_reported_success"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td valign="top">`type`</td>
      <td valign="top">string</td>

      <td>
        Details about the sign-in attempt. Possible values are:

        <ul>
          <li>`"all_blocked"`</li>
          <li>`"anonymous_blocked"`</li>
          <li>`"code_bad"`</li>
          <li>`"code_disabled"`</li>
          <li>`"code_timeout"`</li>
          <li>`"continent_blocked"`</li>
          <li>`"country_blocked"`</li>
          <li>`"credentials_ok"`</li>
          <li>`"duo_bad"`</li>
          <li>`"duo_disabled"`</li>
          <li>`"duo_native_bad"`</li>
          <li>`"duo_timeout"`</li>
          <li>`"federated"`</li>
          <li>`"ip_blocked"`</li>
          <li>`"mfa_missing"`</li>
          <li>`"mfa_ok"`</li>
          <li>`"modern_version_missing"`</li>
          <li>`"modern_version_old"`</li>
          <li>`"non_sso_user"`</li>
          <li>`"password_secret_bad"`</li>
          <li>`"platform_secret_bad"`</li>
          <li>`"platform_secret_disabled"`</li>
          <li>`"platform_secret_proxy"`</li>
          <li>`"service_account_sso_denied"`</li>
          <li>`"sso_user_mismatch"`</li>
          <li>`"totp_bad"`</li>
          <li>`"totp_disabled"`</li>
          <li>`"totp_timeout"`</li>
          <li>`"u2f_bad"`</li>
          <li>`"u2f_disabled"`</li>
          <li>`"u2f_timeout"`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td>`country`</td>
      <td>string</td>
      <td>The country code of the event. Uses the [ ISO 3166 standard](https://www.iso.org/iso-3166-country-codes.html).</td>
    </tr>

    <tr>
      <td>`details`</td>
      <td>object</td>
      <td> A [details object](#details-object-schema) that contains additional information about the sign-in attempt.</td>
    </tr>

    <tr>
      <td>`target_user`</td>
      <td>object</td>
      <td>A [user object](#userv2-object-schema-2).</td>
    </tr>

    <tr>
      <td>`client`</td>
      <td>object</td>
      <td>A [client object](#client-object-schema-1).</td>
    </tr>

    <tr>
      <td>`location`</td>
      <td>object</td>
      <td>A [location object](#location-object-schema-2) that contains details about the geolocation of the client based on the client's IP address at the time the event was performed.</td>
    </tr>

    <tr>
      <td>`account_uuid`\
      (MSP accounts only)</td>
      <td>string</td>
      <td>The UUID of the account where the action was performed.</td>
    </tr>
  </tbody>
</table>

###### Details object schema

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
      <td>`value`</td>
      <td>string</td>
      <td>The additional information about the sign-in attempt, such as any firewall rules that prevent a user from signing in. For example, in the event of a sign-in attempt blocked by firewall rules, the value is the country, continent, or IP address of the sign-in attempt.</td>
    </tr>
  </tbody>
</table>

###### UserV2 object schema

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
      <td>`uuid`</td>
      <td>string</td>
      <td>The UUID of the user that accessed the item or attempted to sign in to the account.</td>
    </tr>

    <tr>
      <td>`name`</td>
      <td>string</td>
      <td>The name of the user, hydrated at the time the event was generated.</td>
    </tr>

    <tr>
      <td>`email`</td>
      <td>string</td>
      <td>The email address of the user, hydrated at the time the event was generated.</td>
    </tr>

    <tr>
      <td valign="top">`user_type`\
      (MSP accounts only)</td>
      <td valign="top">string</td>

      <td>
        The type of user who performed the action (internal or external). Possible values are:

        <ul>
          <li>`user`</li>
          <li>`external_user`</li>
        </ul>
      </td>
    </tr>

    <tr>
      <td>`user_account_uuid`\
      (MSP accounts only)</td>
      <td>string</td>
      <td>The UUID of the user's account.</td>
    </tr>
  </tbody>
</table>

###### Client object schema

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
      <td>`app_name`</td>
      <td>string</td>
      <td>The name of the 1Password app the item was accessed from.</td>
    </tr>

    <tr>
      <td>`app_version`</td>
      <td>string</td>
      <td>The version number of the app.</td>
    </tr>

    <tr>
      <td>`platform_name`</td>
      <td>string</td>
      <td>The name of the platform the item was accessed from.</td>
    </tr>

    <tr>
      <td>`platform_version`</td>
      <td>string</td>
      <td>The version of the browser or computer where 1Password is installed or the CPU of the machine where the 1Password command-line tool is installed.</td>
    </tr>

    <tr>
      <td>`os_name`</td>
      <td>string</td>
      <td>The name of the operating system the item was accessed from.</td>
    </tr>

    <tr>
      <td>`os_version`</td>
      <td>string</td>
      <td>The version of the operating system the item was accessed from.</td>
    </tr>

    <tr>
      <td>`ip_address`</td>
      <td>string</td>
      <td>The IP address the item was accessed from.</td>
    </tr>
  </tbody>
</table>

###### Location object schema

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
      <td>`country`</td>
      <td>string</td>
      <td>The country where the sign-in attempt was made.</td>
    </tr>

    <tr>
      <td>`region`</td>
      <td>string</td>
      <td>The region where the sign-in attempt was made.</td>
    </tr>

    <tr>
      <td>`city`</td>
      <td>string</td>
      <td>The city where the sign-in attempt was made.</td>
    </tr>

    <tr>
      <td>`longitude`</td>
      <td>number</td>
      <td>A coordinate that specifies the longitudinal location where the sign-in attempt was made.</td>
    </tr>

    <tr>
      <td>`latitude`</td>
      <td>number</td>
      <td>A coordinate that specifies the latitudinal location where the sign-in attempt was made.</td>
    </tr>
  </tbody>
</table>

## ErrorResponse object

**Example error response**

```json theme={null}
{
	status: 401,
	message: "Unauthorized access"
}
```

**ErrorResponse object schema**

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
      <td>`status`</td>
      <td>integer</td>
      <td>The HTTP status code.</td>
    </tr>

    <tr>
      <td>`message`</td>
      <td>string</td>
      <td>A message detailing the error.</td>
    </tr>
  </tbody>
</table>


## Related topics

- [1Password Events API](/events-api.md)
- [Get started with the 1Password Events API](/events-api/get-started.md)
- [1Password Events API changelog](/events-api/changelog.md)

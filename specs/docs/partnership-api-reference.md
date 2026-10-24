> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Partnership API reference

You can use the 1Password Partnership API to manage the provisioning and deprovisioning of third-party partner billing accounts for your customers. The API supports partner billing accounts for 1Password individual and family accounts. The Partnership API doesn't support 1Password team or business accounts.

> [!NOTE]
>
> To work with the 1Password Partnership API in another tool, download the API specification file: [1password-partnership-api.yml](https://i.1password.com/media/1password-partnership-api/partnership-api.yml).

## Prerequisites

Before you can use the API to integrate with our partner billing service, you'll need to register as a 1Password partner. Registered partners are granted access to bearer tokens to authorize requests to the API billing servers.

To learn more about our partnership opportunities, visit the [1Password Partner Program website](https://1password.com/partnerships) or [contact the 1Password Partnerships team](mailto:partners@1password.com). If you're not a partner, reach out to your Customer Success Manager or the [1Password Sales team](https://1password.com/contact-us).

## Information about the API

The 1Password Partnership API is a REST-style API that follows the [OpenAPI 3.0 specifications](https://spec.openapis.org/oas/v3.0.3). All communication between clients and servers is over HTTPS.

You can use your preferred programming language and tools for testing and implementing the Partnership API. This reference uses [curl](https://curl.se/) on the command line to demonstrate example requests. You can replace the values in any request with your own to receive information about your customers' billing accounts.

### Request methods

You can use the following standard HTTP methods to make requests to the Partnership API:

* [POST](#create-a-billing-account): Create a third-party billing account for a customer through the partner billing service.
* [GET](#get-billing-account-information): Get details about a customer's billing account.
* [DELETE](#delete-a-billing-account): Delete a customer's third-party billing account from the partner billing service.
* [PATCH](#update-a-billing-account-end-date): Update the date and time a customer's billing account is scheduled be removed from the partner billing service.

Batch requests are not supported.

### Servers

There are two billing servers partners can use to work with the 1Password Partnership API that provide the base URLs of the API endpoints:

* **Test server** (`https://billing.b5test.eu`): Use the test server URL as the base for all requests in the test environment. You can provision and deprovision test partner billing accounts for all domains from the test server: **b5test.com**, **b5test.ca**, and **b5test.eu.**
* **Production server** (`https://billing.1password.com`): Use the production server URL as the base for all requests in the production environment. You can provision and deprovision partner billing accounts for all domains from the production server: **1password.com**, **1password.ca**, and **1password.eu**.

### Endpoints

Each request to the API starts with the base URL of the server environment you want to work with (test or production), followed by the path (`api/v1/partners/account`). Path parameters, indicated with curly braces (`{}`), are required where defined. For example:

```text Structure of an API endpoint theme={null}
<base_URL>/<path>/{parameters}
```

Replace the `base_URL` and `\{parameters}` placeholders with the server environment you're using and any path parameters specified for the request. The `path` is the same for all requests.

```text Example API endpoint without a path parameter theme={null}
https://billing.b5test.eu/api/v1/partners/accounts
```

```text Example API endpoint with a path parameter theme={null}
https://billing.1password.com/api/v1/partners/accounts/4266474b-6385-56d4-7b75-648096593064
```

### Authorization

When you register with the [1Password Partner Program](https://1password.com/partnerships), the Partnership team will provide you with bearer tokens you'll need to authorize your calls to the Partnership API.

You'll receive separate tokens to use with [the test and production environments](#servers). Make sure to use the token that has been authorized for the environment you're working in.

If you're a partner and need a new bearer token, [contact the 1Password Partnerships team](mailto:partners@1password.com). If you're not a partner, reach out to your Customer Success Manager or the [1Password Sales team](https://1password.com/contact-us) and let them know you need a new bearer token.

### Request headers

Requests to the Partnership API use three types of headers:

* **`Authorization`**: Each GET, POST, DELETE, and PATCH request to the Partnership API must be authorized with a [bearer token](#authorization).
* **`Content-Type`**: Each POST and PATCH request requires a header to indicate the media (MIME) type of the request body.
* **`Accept`**: Each GET, POST, and PATCH request to the Partnership API should include an accept header to indicate what kind of response the client can accept from the server.

All data is sent and received as JSON, so make sure to specify that in the headers.

```
Authorization: Bearer YOUR_BEARER_TOKEN
Content-type: application/json
Accept: application/json
```

If you're a partner and need a new bearer token, [contact the 1Password Partnerships team](mailto:partners@1password.com). If you're not a partner, reach out to your Customer Success Manager or the [1Password Sales team](https://1password.com/contact-us) and let them know you need a new bearer token.

### Request bodies

Request bodies (also called request payloads) contain the JSON-formatted data clients send to create (POST) or update (PATCH) resources on the servers. A request body consists of an object that may include one or more of the following fields, as indicated:

* **The unique identifier (UID) for the customer's billing account.** The UID is supplied by the partner. It can be up to 200 characters long with any combination of alphanumeric characters (`A-Z`, `a-z`, `0-9`), hyphens (`-`), and periods/dots (`.`) .
* **The 1Password account type.** Options are individual (`I`) or family (`F`). Team and business accounts aren't supported.
* **The domain the customer can use for their new or existing 1Password account.** For the test server, options are: `b5test.com`, `b5test.ca`, or `b5test.eu`. For the production server, options are: `1password.com`, `1password.ca`, or `1password.eu`.
* **The date and time the customer's billing account is scheduled to be removed.** The date cannot be in the past. Format the date, time, and optional timezone in the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html).

GET and DELETE calls don't contain request bodies.

### Activation tokens

Activation tokens are used to provision customers to third-party billing by linking 1Password accounts to partner billing accounts.

Make a [POST call](#create-a-billing-account) to the Partnership API to create a new partner billing account for a customer and generate their unique activation token.

Append the token returned in the [POST response](#success-response) to a 1Password partnership redemption link to [create the customer's partner billing link](#create-a-link-for-customers). Then provide the customer with their link.

The link will direct the customer to a promotional page where they'll be instructed to create a new 1Password account or sign in to an existing one. The billing for the customer's 1Password account will then be linked to their partner billing account.

#### Create a link for customers

To create a partner billing link for a customer, adjust the 1Password partnership redemption link (`https://start.[1password_domain]/partnership/redeem`) to use the desired 1Password domain. Then append a query string with the required parameters for the account type and the activation token. You can also include an optional [language parameter](#language-codes).

```text Structure of a partner billing link for a 1Password account theme={null}
https://start.[1password_domain]/partnership/redeem?t={account_type}&c={activation_token}&l={language_code}
```

| Placeholder | Values | Required |
| - | - | - |
| `[1password_domain]` | Possible values are `1password.com`, `1password.ca`, or `1password.eu`. | Yes |
| `\{account_type}` | Possible values are `individual` or `family`. | Yes |
| `\{activation_token}` | The value of the token returned in the [POST response](#success-response). For example: `4266474b-6385-56d4-7b75-648096593064`. | Yes |
| `\{language_code}` | Optional [language code](#language-codes) values are `en`, `de`, `es`, `fr`, `it`, `ja`, `ko`, `nl`, `pt-BR`, `ru`, `zh-Hans`, or `zh-Hant`. | No |

Replace the placeholders for the 1Password domain and the parameters with the appropriate values. For example:

```text Example partner billing link for an individual account on 1Password.eu with an optional language parameter theme={null}
https://start.1password.eu/partnership/redeem?t=individual&c=4266474b-6385-56d4-7b75-648096593064&l=de
```

```text Example partner billing link for a family account on 1Password.com with an optional language parameter theme={null}
https://start.1password.com/partnership/redeem?t=family&c=4266474b-6385-56d4-7b75-648096593064&l=en
```

If you're not sure which link(s) you need to create, [contact the 1Password Partnerships team](mailto:partners@1password.com) for help.

> [!WARNING]
>
> For customers with existing 1Password accounts, partner billing links will only work if their 1Password account type and domain are the same as the ones specified in the [POST request](#request).
>
> Customers can [contact 1Password Support](https://support.1password.com/contact/) if they need help to change their existing [account type](https://support.1password.com/change-account-type/) or [region](https://support.1password.com/regions/#change-your-region).

#### Alternate partner billing link options

> **Learn more about alternate link options.**
>
> The Partnership API also supports some alternate options for partner billing links. Depending on the details of your partnership, you might prefer to create links for:
>
> * [A new 1Password account only](#create-a-link-for-a-new-1password-account-only)
> * [An existing 1Password account only](#create-a-link-for-an-existing-1password-account-only)
>
> If you provide a customer with one of these links, they'll only be able to use it to link their partner billing account to either a new account or an existing account, respectively.
>
> You can also create one link of each type and provide your customers with both so they can choose the option they need. However, creating a [single link](#create-a-link-for-customers) that allows for both options may be a better solution.
>
> ##### Create a link for a new 1Password account only
>
> To create a partner billing link for a customer to use with a new 1Password account only, adjust the 1Password sign-up link (`https://start.[1password_domain]/sign-up/[account_type]`) to use the desired 1Password domain and account type. Then append a query string with the required activation token parameter. You can also include an optional [language parameter](#language-codes). For example:
>
> ```text Structure of a partner billing link for a new 1Password account theme={null}
> https://start.[1password_domain]/sign-up/[account_type]?c={activation_token}&l={language_code}
> ```
>
> Replace the [placeholders](#placeholder-reference) for the 1Password domain, account type, and the parameters with the appropriate values. For example:
>
> ```text Example partner billing link for a new individual account on 1Password.eu with an optional language parameter theme={null}
> https://start.1password.eu/sign-up/individual?c=4266474b-6385-56d4-7b75-648096593064&l=de
> ```
>
> ```text Example partner billing link for a new family account on 1Password.com with an optional language parameter theme={null}
> https://start.1password.com/sign-up/family?c=4266474b-6385-56d4-7b75-648096593064&l=en
> ```
>
> ##### Create a link for an existing 1Password account only
>
> To create a partner billing link for a customer to use with an existing 1Password account only, adjust the 1Password account sign-in link (`https://my.[1password_domain]/partnership/link`) to use the desired 1Password domain. Then append a query string with the required activation token parameter. You can also include an optional [language parameter](#language-codes). For example:
>
> ```text Structure of a partner billing link for an existing 1Password account theme={null}
> https://my.[1password_domain]/partnership/link?c={activation_token}&l={language_code}
> ```
>
> Replace the [placeholders](#placeholder-reference) for the 1Password domain and the parameters with the appropriate values. For example:
>
> ```text Example partner billing link for an existing account on 1Password.com with an optional language parameter theme={null}
> https://my.1password.com/partnership/link?c=4266474b-6385-56d4-7b75-648096593064&l=en
> ```
>
> This link doesn't include an account type because it directs the customer to sign in to the 1Password account they already have.
>
> ##### Placeholder reference
>
> | Placeholder | Values | Required |
> | - | - | - |
> | `[1password_domain]` | Possible values are `1password.com`, `1password.ca`, or `1password.eu`. | Yes |
> | `[account_type]` | The account type is only used with links to [create a new 1Password account](#create-a-link-for-a-new-1password-account-only). Possible values are `individual` or `family`. | Yes For new accounts only |
> | `\{activation_token}` | The value of the token returned in the [POST response](#success-response). For example: `4266474b-6385-56d4-7b75-648096593064`. | Yes |
> | `\{language_code}` | Optional [language code](#language-codes) values are `en`, `de`, `es`, `fr`, `it`, `ja`, `ko`, `nl`, `pt-BR`, `ru`, `zh-Hans`, or `zh-Hant`. | No |

#### Language code parameters

> **Learn more about optional language code parameters.**
>
> Language codes are an optional parameter that can be added to a [partner billing link](#create-a-link-for-customers) to take the customer to the appropriate landing page for that language. Customers will be directed to the default landing page (English) if no language parameter is included.
>
> The following language codes can be used with the language parameter:
>
> | Language | Code |
> | - | - |
> | English | `en` |
> | Deutsch | `de` |
> | Español | `es` |
> | Français | `fr` |
> | Italiano | `it` |
> | 日本語 | `ja` |
>
> | Language | Code |
> | - | - |
> | 한국어 | `ko` |
> | Nederlands | `nl` |
> | Português | `pt-BR` |
> | Русский | `ru` |
> | 简体中文 | `zh-Hans` |
> | 繁體中文 | `zh-Hant` |

## Create a billing account

```
POST <base_URL>/api/v1/partners/accounts
```

A POST call creates a new third-party billing account for a customer through the partner billing service and returns the [activation token](#activation-tokens) you'll use to create a partner billing link for the customer to complete provisioning.

### Path parameters

No path parameters.

### Request

Use the endpoint URL with your [bearer token](#authorization) and the required [request headers](#request-headers). Include an object as a [request body](#request-bodies) that contains:

* The customer's account UID.
* The eligible 1Password account type.
* The domain the customer can use for their new or existing 1Password account.
* (Optional) The date and time you want to remove the customer's account from the partner billing service. This value cannot be in the past. You can also update this field with [a PATCH request](#update-a-billing-account-end-date).

**Example request**

```shell highlight={7,8,9,10} theme={null}
curl --request POST \
    --url https://billing.1password.com/api/v1/partners/accounts \
    --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
    --header 'Content-Type: application/json' \
    --header 'Accept: application/json' \
    --data '{
        "customer_account_uid": "4266474b-6385-56d4-7b75-648096593064",
        "account_type": "F",
        "domain": "1password.com",
        "ends_at": "2024-08-31T13:00:00-05:00"
    }'
```

**Request object schema**

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
      <th>`customer_account_uid`\
      Required</th>
      <td>string</td>
      <td>The unique identifier (UID) for the customer's billing account. The UID can be up to 80 characters long with any combination of alphanumeric characters and hyphens.</td>
    </tr>

    <tr>
      <th>`account_type`\
      Required</th>
      <td>string</td>
      <td>The type of 1Password account you want to provision for the customer: `I` for individual account or `F` for family account. Team and business accounts aren't supported.</td>
    </tr>

    <tr>
      <th>`domain`\
      Required</th>
      <td>string</td>
      <td> The domain for the new or existing 1Password account the customer can use with the partner billing service. For the test server: `b5test.com`, `b5test.ca`, or `b5test.eu`. For the production server: `1password.com`, `1password.ca`, or `1password.eu`.</td>
    </tr>

    <tr>
      <th>`ends_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account is scheduled to be removed from the partner billing service. Uses the format defined by RFC 3339.</td>
    </tr>
  </tbody>
</table>

### Success response

A `201` response returns an Account object containing the unique [activation token](#activation-tokens) that's used to link the customer's 1Password account with their partner billing account.

**Example response**

```json highlight={4} theme={null}
{
	"customer_account_uid": "4266474b-6385-56d4-7b75-648096593064",
	"account_type": "F",
	"activation_token": "PNS-D5A75BT2",
	"domain": "1password.com",
	"status": "entitled",
	"deployed_members": 0,
	"created_at": "2023-08-24T04:19:44Z",
	"updated_at": "2023-09-15T15:58:22Z",
	"ends_at": "2024-08-31T18:00:00Z"
}
```

**Response object schema**

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
      <th>`customer_account_uid`</th>
      <td>string</td>
      <td>The unique identifier (UID) for the customer's billing account. The UID can be up to 80 characters long with any combination of alphanumeric characters and hyphens.</td>
    </tr>

    <tr>
      <th>`account_type`</th>
      <td>string</td>
      <td>The type of 1Password account you provisioned for the customer: `I` for individual account or `F` for family account.</td>
    </tr>

    <tr>
      <th>`activation_token`</th>
      <td>string</td>
      <td>The [activation token](#activation-tokens) you'll use to create a link that connects the customer's new or existing 1Password account to their partner billing account (for example, `https://start.[1password_domain]/partnership/redeem?t=[account_type]&c=[activation_token]&l=[language_code]`). Tokens can only be used with a 1Password individual or family account. Team and business accounts aren't supported.</td>
    </tr>

    <tr>
      <th>`domain`</th>
      <td>string</td>
      <td>The domain for the new or existing 1Password account the customer can use with the partner billing service. For the test server: `b5test.com`, `b5test.ca`, or `b5test.eu`. For the production server: `1password.com`, `1password.ca`, or `1password.eu`.</td>
    </tr>

    <tr>
      <th>`status`</th>
      <td>string</td>

      <td>
        The status of the customer billing account that was provisioned. Possible values are one of:

        <ul>
          <li>`entitled` Provisioning has been initiated, but the customer hasn't used their partner billing link with a new or existing 1Password account.</li>
          <li>`provisioned` The customer has used their partner billing link with a new or existing 1Password account, and their third-party billing account has been provisioned.</li>
        </ul>

        The expected value for the POST request is `entitled`.
      </td>
    </tr>

    <tr>
      <th>`deployed_members`</th>
      <td>integer</td>
      <td>The number of provisioned users in the 1Password account. \
      The expected value returned for a POST request is `0`.</td>
    </tr>

    <tr>
      <th>`created_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account was created. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html).</td>
    </tr>

    <tr>
      <th>`updated_at`</th>
      <td>string</td>
      <td>The date and time the billing account was last updated. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html). This field is updated when there are account status changes. \
      The expected value returned for a POST request is the same as the value of the `created_at` property.</td>
    </tr>

    <tr>
      <th>`ends_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account is scheduled to be removed from the partner billing service. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html). If a date and time haven't been specified, the expected value is `null`.</td>
    </tr>
  </tbody>
</table>

### Error responses

> **Example error**
>
> ```json theme={null}
> {
>   "code": 400,
>   "error": "bad_request",
>   "description": "Account type B is not supported."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine-parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 403,
>   "error": "forbidden",
>   "description": "Invalid auth token."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine-parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 404,
>   "error": "not_found",
>   "description": "Domain not found."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine-parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 500,
>   "error": "internal_server_error",
>   "description": "Internal server error"
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

## Get billing account information

```
GET <base_URL>/api/v1/partners/accounts/{customer_account_uid}
```

A GET call retrieves information about a customer's billing account. Make sure to include the customer's account UID as a path parameter.

### Path Parameters

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
      <th>`customer_account_uid`\
      Required</th>
      <td>string</td>
      <td>The unique ID of the customer's billing account.</td>
    </tr>
  </tbody>
</table>

### Request

Use the endpoint URL with your [bearer token](#authorization) and the required [request headers](#request-headers) to request a customer's billing account information. The GET request doesn't include a body, so the `Content-type` header isn't used.

**Example request**

```shell theme={null}
curl --request GET \
    --url https://billing.1password.com/api/v1/partners/accounts/{customer_account_uid} \
    --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
    --header 'Accept: application/json'
```

### Success response

A `200` response returns an Account object that provides information about the customer's third-party billing account.

**Example response**

```json theme={null}
{
	"customer_account_uid": "4266474b-6385-56d4-7b75-648096593064",
	"account_type": "F",
	"activation_token": "PNS-D5A75BT2",
	"domain": "1password.com",
	"status": "provisioned",
	"deployed_members": 1,
	"created_at": "2023-08-24T04:19:44Z",
	"updated_at": "2023-09-15T15:58:22Z",
	"ends_at": "2024-08-31T18:00:00Z"
}
```

**Response object schema**

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
      <th>`customer_account_uid`</th>
      <td>string</td>
      <td>The unique identifier (UID) for the customer's billing account. The UID can be up to 80 characters long with any combination of alphanumeric characters and hyphens.</td>
    </tr>

    <tr>
      <th>`account_type`</th>
      <td>string</td>
      <td>The type of 1Password account you provisioned for the customer: `I` for individual account or `F` for family account.</td>
    </tr>

    <tr>
      <th>`activation_token`</th>
      <td>string</td>
      <td>The [activation token](#activation-tokens) you'll use to create a link that connects the customer's new or existing 1Password account to their partner billing account (for example, `https://start.[1password_domain]/partnership/redeem?t=[account_type]&c=[activation_token]&l=[language_code]`). Tokens can only be used with a 1Password individual or family account. Team and business accounts aren't supported.</td>
    </tr>

    <tr>
      <th>`domain`</th>
      <td>string</td>
      <td>The domain for the new or existing 1Password account the customer can use with the partner billing service. For the test server: `b5test.com`, `b5test.ca`, or `b5test.eu`. For the production server: `1password.com`, `1password.ca`, or `1password.eu`.</td>
    </tr>

    <tr>
      <th>`status`</th>
      <td>string</td>

      <td>
        The status of the customer billing account that was provisioned. Possible values are one of:

        <ul>
          <li>`entitled` Provisioning has been initiated, but the customer hasn't used their partner billing link with a new or existing 1Password account.</li>
          <li>`provisioned` The customer has used their partner billing link with a new or existing 1Password account, and their third-party billing account has been provisioned.</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`deployed_members`</th>
      <td>integer</td>
      <td>The number of provisioned users in the 1Password account.</td>
    </tr>

    <tr>
      <th>`created_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account was created. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html).</td>
    </tr>

    <tr>
      <th>`updated_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account was last updated. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html). This field updates when there are account status changes.</td>
    </tr>

    <tr>
      <th>`ends_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account is scheduled to be removed from the partner billing service. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html). If a date and time haven't been specified, the expected value is `null`.</td>
    </tr>
  </tbody>
</table>

### Error responses

> **Example error**
>
> ```json theme={null}
> {
>   "code": 403,
>   "error": "forbidden",
>   "description": "Invalid auth token."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 404,
>   "error": "not_found",
>   "description": "Failed to find the requested account."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 410,
>   "error": "gone",
>   "description": "The requested account is gone."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 500,
>   "error": "internal_server_error",
>   "description": "Internal server error"
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

## Delete a billing account

```
DELETE <base_URL>/api/v1/partners/accounts/{customer_account_uid}
```

A DELETE call removes a customer's third-party billing account from the partner billing service. Make sure to include the customer's account UID as a path parameter.

### Path Parameters

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
      <th>`customer_account_uid`\
      Required</th>
      <td>string</td>
      <td>The unique ID of the customer billing account to remove.</td>
    </tr>
  </tbody>
</table>

### Request

Use the endpoint URL with your [bearer token](#authorization) for the required [request header](#request-headers) to remove a customer's billing account. The DELETE request and subsequent response don't include a body, so the `Content-type` and `Accept` headers aren't used.

**Example request**

```shell theme={null}
curl --request DELETE \
    --url https://billing.1password.com/api/v1/partners/accounts/{customer_account_uid} \
    --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
```

### Success response

A `204` response is returned on successful deactivation and removal of a customer billing account. A GET request will no longer return account information for the customer account UID because the billing account has been deleted.

### Error responses

> **Example error**
>
> ```json theme={null}
> {
>   "code": 403,
>   "error": "forbidden",
>   "description": "Invalid auth token."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example value**
>
> ```json theme={null}
> {
>   "code": 404,
>   "error": "not_found",
>   "description": "Failed to find the requested account."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 500,
>   "error": "internal_server_error",
>   "description": "Internal server error"
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

## Update a billing account end date

```
PATCH <base_URL>/api/v1/partners/accounts/{customer_account_uid}
```

A PATCH call lets you add, edit, or remove the end date for a customer's billing account. Make sure to include the customer's account UID as a path parameter.

### Path Parameters

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
      <th>`customer_account_uid`\
      Required</th>
      <td>string</td>
      <td>The unique ID of the customer's billing account.</td>
    </tr>
  </tbody>
</table>

### Request

Use the endpoint URL with your [bearer token](#authorization) and the required [request headers](#request-headers). Include an object as a [request body](#request-bodies) that contains the `ends_at` field.

To add or update the date and time a customer's billing account is scheduled to be removed from the partnership billing service, include a new date and time as the `ends_at` value, in the format defined by RFC 3339.

To remove the end date and time from a customer's billing account, use an empty string (`""`) or `null` as the value.

**Example request**

```shell highlight={6} theme={null}
curl --request PATCH \
    --url https://billing.1password.com/api/v1/partners/accounts/{customer_account_uid} \
    --header 'Authorization: Bearer YOUR_BEARER_TOKEN' \
    --header 'Accept: application/json' \
    --data '{
        "ends_at": "2024-08-31T13:00:00-05:00"
    }'
```

**Request object schema**

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
      <th>`ends_at`\
      Required</th>
      <td>string</td>
      <td> The date and time the customer's billing account is scheduled to be removed from the partner billing service. Uses the format defined by RFC 3339. If you want to remove the end date from an account, use an empty string (`""`) or `null` as the value.</td>
    </tr>
  </tbody>
</table>

### Success response

A `200` response returns an Account object that provides information about the customer's third-party billing account, including the end date that has been added, edited, or removed from the billing account.

**Example response**

```json highlight={10} theme={null}
{
	"customer_account_uid": "4266474b-6385-56d4-7b75-648096593064",
	"account_type": "F",
	"activation_token": "PNS-D5A75BT2",
	"domain": "1password.com",
	"status": "provisioned",
	"deployed_members": 1,
	"created_at": "2023-08-24T04:19:44Z",
	"updated_at": "2023-09-15T15:58:22Z",
	"ends_at": "2024-08-31T18:00:00Z"
}
```

**Response object schema**

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
      <th>`customer_account_uid`</th>
      <td>string</td>
      <td>TThe unique identifier (UID) for the customer's billing account. The UID can be up to 80 characters long with any combination of alphanumeric characters and hyphens.</td>
    </tr>

    <tr>
      <th>`account_type`</th>
      <td>string</td>
      <td>The type of 1Password account you provisioned for the customer: `I` for individual account or `F` for family account.</td>
    </tr>

    <tr>
      <th>`activation_token`</th>
      <td>string</td>
      <td>The [activation token](#activation-tokens) you'll use to create a link that connects the customer's new or existing 1Password account to their partner billing account (for example, `https://start.[1password_domain]/partnership/redeem?t=[account_type]&c=[activation_token]&l=[language_code]`). Tokens can only be used with a 1Password individual or family account. Team and business accounts aren't supported.</td>
    </tr>

    <tr>
      <th>`domain`</th>
      <td>string</td>
      <td>The domain for the new or existing 1Password account the customer can use with the partner billing service. For the test server: `b5test.com`, `b5test.ca`, or `b5test.eu`. For the production server: `1password.com`, `1password.ca`, or `1password.eu`.</td>
    </tr>

    <tr>
      <th>`status`</th>
      <td>string</td>

      <td>
        The status of the customer's billing account. Possible values are one of:

        <ul>
          <li>`entitled` Provisioning has been initiated, but the customer hasn't used their partner billing link with a new or existing 1Password account.</li>
          <li>`provisioned` The customer has used their partner billing link with a new or existing 1Password account, and their third-party billing account has been provisioned.</li>
        </ul>
      </td>
    </tr>

    <tr>
      <th>`deployed_members`</th>
      <td>integer</td>
      <td>The number of provisioned users in the 1Password account.</td>
    </tr>

    <tr>
      <th>`created_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account was created. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html).</td>
    </tr>

    <tr>
      <th>`updated_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account was last updated. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html). This field updates when there are account status changes.</td>
    </tr>

    <tr>
      <th>`ends_at`</th>
      <td>string</td>
      <td>The date and time the customer's billing account is scheduled to be removed from the partner billing service. Uses the [ISO 8601 standard](https://www.iso.org/iso-8601-date-and-time-format.html). If a date and time haven't been specified, the expected value is `null`.</td>
    </tr>
  </tbody>
</table>

### Error responses

> **Example error**
>
> ```json theme={null}
> {
>   "code": 403,
>   "error": "forbidden",
>   "description": "Invalid auth token."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 404,
>   "error": "not_found",
>   "description": "Failed to find the requested account."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 410,
>   "error": "gone",
>   "description": "The requested account is gone."
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>

> **Example error**
>
> ```json theme={null}
> {
>   "code": 500,
>   "error": "internal_server_error",
>   "description": "Internal server error"
> }
> ```
>
> **Error object schema**
>
> <table>
>   <thead>
>     <tr>
>       <th>Parameter</th>
>       <th>Type</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>
>   <tbody>
>     <tr>
>       <th>`code`</th>
>       <td>integer</td>
>       <td>The HTTP response code of the error.</td>
>     </tr>
>
>     <tr>
>       <th>`error`</th>
>       <td>string</td>
>       <td>A machine parsable string that represents the code.</td>
>     </tr>
>
>     <tr>
>       <th>`description`</th>
>       <td>string</td>
>       <td>A description of the error.</td>
>     </tr>
>   </tbody>
> </table>


## Related topics

- [1Password Accounts API for Partners](https://www.1password.dev/accounts-api.md)
- [1Password Events API reference](https://www.1password.dev/events-api/reference.md)
- [1Password Users API reference](https://www.1password.dev/users-api/reference.md)

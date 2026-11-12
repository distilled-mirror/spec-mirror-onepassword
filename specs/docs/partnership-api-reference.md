> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Partnership API reference

As a 1Password partner, use the Partnership API to create, look up, change the end date of, and delete the billing accounts you provide to your customers. It works with 1Password individual and family accounts, not team or business accounts.

> [card] [Try the API in your browser](https://www.1password.dev/partnership-api/reference/get-billing-account.md)
>
> Paste your token, pick the test server, and send a real request from the docs. No code needed.

> [!NOTE]
>
> To work with the 1Password Partnership API in another tool, download the API specification file: [1password-partnership-api.yml](https://i.1password.com/media/1password-partnership-api/partnership-api.yml).

## Prerequisites

Before you can use the API to integrate with our partner billing service, you'll need to register as a 1Password partner. Registered partners are granted access to bearer tokens to authorize requests to the API billing servers.

To learn more about our partnership opportunities, visit the [1Password Partner Program website](https://1password.com/partnerships) or [contact the 1Password Partnerships team](mailto:partners@1password.com). If you're not a partner, reach out to your Customer Success Manager or the [1Password Sales team](https://1password.com/contact-us).

## Try it in the docs

Every endpoint has a **Try it** panel. Paste your bearer token and send a real request from your browser.

**1. Use your test token**

The Partnership team gives you separate tokens for the test and production environments. Use your test token while you explore. See [Authorization](#authorization).

**2. Start with a read-only call**

Open [Get billing account information](https://www.1password.dev/partnership-api/reference/get-billing-account.md). It reads an account without changing it.

**3. Pick the test server and paste your token**

Select **Try it**, choose the test server (billing.b5test.eu), and paste your token in the **Authorization** field.

**4. Send it, then copy the code**

Select **Send** to see the live response. The example panel turns the same request into cURL, Python, JavaScript, Go, and more.

> [!WARNING]
>
> Create, update, and delete calls change real billing accounts. Only send them to production when you mean to.

## Information about the API

The 1Password Partnership API is a REST-style API that follows the [OpenAPI 3.0 specifications.](https://spec.openapis.org/oas/v3.0.3) All communication between clients and servers is over HTTPS.

You can use your preferred programming language and tools for testing and implementing the Partnership API. This reference uses [curl](https://curl.se/) on the command line to demonstrate example requests. You can replace the values in any request with your own to receive information about your customers' billing accounts.

### Request methods

You can use the following standard HTTP methods to make requests to the Partnership API:

* [POST](https://www.1password.dev/partnership-api/reference/create-billing-account.md): Create a third-party billing account for a customer through the partner billing service.
* [GET](https://www.1password.dev/partnership-api/reference/get-billing-account.md): Get details about a customer's billing account.
* [DELETE](https://www.1password.dev/partnership-api/reference/delete-billing-account.md): Delete a customer's third-party billing account from the partner billing service.
* [PATCH](https://www.1password.dev/partnership-api/reference/update-billing-account-end-date.md): Update the date and time a customer's billing account is scheduled be removed from the partner billing service.

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
* **The date and time the customer's billing account is scheduled to be removed.** The date cannot be in the past. Format the date, time, and optional timezone in the [ISO 8601 standard.](https://www.iso.org/iso-8601-date-and-time-format.html)

GET and DELETE calls don't contain request bodies.

### Activation tokens

Activation tokens are used to provision customers to third-party billing by linking 1Password accounts to partner billing accounts.

Make a [POST call](https://www.1password.dev/partnership-api/reference/create-billing-account.md) to the Partnership API to create a new partner billing account for a customer and generate their unique activation token.

Append the token returned in the [POST response](https://www.1password.dev/partnership-api/reference/create-billing-account.md) to a 1Password partnership redemption link to [create the customer's partner billing link](#create-a-link-for-customers). Then provide the customer with their link.

The link will direct the customer to a promotional page where they'll be instructed to create a new 1Password account or sign in to an existing one. The billing for the customer's 1Password account will then be linked to their partner billing account.

#### Create a link for customers

To create a partner billing link for a customer, adjust the 1Password partnership redemption link (`https://start.[1password_domain]/partnership/redeem`) to use the desired 1Password domain. Then append a query string with the required parameters for the account type and the activation token. You can also include an optional [language parameter](#language-code-parameters).

```text Structure of a partner billing link for a 1Password account theme={null}
https://start.[1password_domain]/partnership/redeem?t={account_type}&c={activation_token}&l={language_code}
```

| Placeholder | Values | Required |
| - | - | - |
| `[1password_domain]` | Possible values are `1password.com`, `1password.ca`, or `1password.eu`. | Yes |
| `\{account_type}` | Possible values are `individual` or `family`. | Yes |
| `\{activation_token}` | The value of the token returned in the [POST response](https://www.1password.dev/partnership-api/reference/create-billing-account.md). For example: `4266474b-6385-56d4-7b75-648096593064`. | Yes |
| `\{language_code}` | Optional [language code](#language-code-parameters) values are `en`, `de`, `es`, `fr`, `it`, `ja`, `ko`, `nl`, `pt-BR`, `ru`, `zh-Hans`, or `zh-Hant`. | No |

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
> For customers with existing 1Password accounts, partner billing links will only work if their 1Password account type and domain are the same as the ones specified in the [POST request](https://www.1password.dev/partnership-api/reference/create-billing-account.md).
>
> Customers can [contact 1Password Support](https://support.1password.com/contact/) if they need help to change their existing [account type](https://support.1password.com/change-account-type/) or [region](https://support.1password.com/regions/#change-your-region).

#### Alternate partner billing link options

> [accordion] Learn more about alternate link options.
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
> To create a partner billing link for a customer to use with a new 1Password account only, adjust the 1Password sign-up link (`https://start.[1password_domain]/sign-up/[account_type]`) to use the desired 1Password domain and account type. Then append a query string with the required activation token parameter. You can also include an optional [language parameter](#language-code-parameters). For example:
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
> To create a partner billing link for a customer to use with an existing 1Password account only, adjust the 1Password account sign-in link (`https://my.[1password_domain]/partnership/link`) to use the desired 1Password domain. Then append a query string with the required activation token parameter. You can also include an optional [language parameter](#language-code-parameters). For example:
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
> | `\{activation_token}` | The value of the token returned in the [POST response](https://www.1password.dev/partnership-api/reference/create-billing-account.md). For example: `4266474b-6385-56d4-7b75-648096593064`. | Yes |
> | `\{language_code}` | Optional [language code](#language-code-parameters) values are `en`, `de`, `es`, `fr`, `it`, `ja`, `ko`, `nl`, `pt-BR`, `ru`, `zh-Hans`, or `zh-Hant`. | No |

#### Language code parameters

> [accordion] Learn more about optional language code parameters.
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

## Endpoint reference

Each endpoint has its own reference page with parameters, example requests and responses, error codes, and a **Try it** panel:

* [Create a billing account](https://www.1password.dev/partnership-api/reference/create-billing-account.md)
* [Get billing account information](https://www.1password.dev/partnership-api/reference/get-billing-account.md)
* [Delete a billing account](https://www.1password.dev/partnership-api/reference/delete-billing-account.md)
* [Update a billing account end date](https://www.1password.dev/partnership-api/reference/update-billing-account-end-date.md)


## Related topics

- [1Password Users API reference](https://www.1password.dev/users-api/reference.md)
- [1Password Events API reference](https://www.1password.dev/events-api/reference.md)
- [Reference for the 1Password Accounts API for Partners](https://www.1password.dev/accounts-api/reference.md)

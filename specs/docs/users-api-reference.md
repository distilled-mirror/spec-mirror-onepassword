> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Users API reference

> Complete reference documentation for the 1Password Users API (v1) and the OAuth API used to authorize requests.

This reference documents every endpoint in the 1Password Users API (v1), along with the OAuth API endpoints used to authorize requests. Each Users API endpoint page includes the parameters, response schemas, and an interactive playground you can use to send test requests.

For more information about authorization and how the Users API works, review the [API conventions](https://www.1password.dev/users-api/conventions.md).

> [!WARNING]
>
> Integrations with the Users API are not currently supported if you use [automated provisioning](https://support.1password.com/provisioning/) with your 1Password account. You can manage actions, like suspending and reactivating users, through your identity provider.

## Endpoints

### OAuth tokens

* [Request an access token](https://www.1password.dev/users-api/request-access-token.md)
* [Introspect an access token](https://www.1password.dev/users-api/introspect-access-token.md)
* [Revoke an access token](https://www.1password.dev/users-api/revoke-access-token.md)

### Users

* [List users](https://www.1password.dev/users-api/list-users.md)
* [Get a user](https://www.1password.dev/users-api/get-user.md)
* [Suspend a user](https://www.1password.dev/users-api/suspend-user.md)
* [Reactivate a user](https://www.1password.dev/users-api/reactivate-user.md)

## OpenAPI specifications

The Users API and OAuth API follow the [OpenAPI 3.1.0 specification.](https://spec.openapis.org/oas/v3.1.0.html) To work with the APIs in another tool, download the specifications:

* [1Password Users API (v1) specification ](https://www.1password.dev/static/files/users-api/1password-users-api_v1_2026-10-05.yaml)
* [1Password OAuth API (v1) specification ](https://www.1password.dev/static/files/users-api/1password-oauth-api_v1_2026-10-05.yaml)


## Related topics

- [1Password Users API](https://www.1password.dev/users-api.md)
- [1Password Events API reference](https://www.1password.dev/events-api/reference.md)
- [1Password Connect Server API reference](https://www.1password.dev/connect/api-reference.md)

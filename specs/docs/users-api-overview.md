> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Users API

> Connect your 1Password account to a security automation platform with the 1Password Users API.

The 1Password Users API is an enterprise-grade REST-style API built on the OAuth 2.0 framework. Use the API to create security automation integrations between [1Password Enterprise Password Manager](https://1password.com/product/enterprise-password-manager) and your preferred solution, and set up security automation workflows to:

* Automate enforcement of 1Password account access controls.
* Trigger access remediation in response to events reporting or SIEM-detected events, such as suspending or reactivating a user.
* Reduce investigation response time, manual processes, and risks during security incidents.

## Requirements

The Users API requires you to have:

* A [1Password Business](https://1password.com/business-security) or [1Password Enterprise Password Manager](https://1password.com/product/enterprise-password-manager) account (not included with 1Password Teams)
* Permissions to manage integrations in your 1Password account (for example, as an [owner](https://support.1password.com/groups/#owners), [administrator](https://support.1password.com/groups/#administrators), or member of the [Security group](https://support.1password.com/groups/#security))

> [!WARNING]
>
> Integrations with the Users API are not currently supported if you use [automated provisioning](https://support.1password.com/provisioning/) with your 1Password account. You can manage actions, like suspending and reactivating users, through your identity provider.

## Quickstart

[Start here](https://www.1password.dev/users-api/get-started.md) to set up an OAuth integration and send your first authentication request to the Users API.

## Guides

* [Authorize your integrations using OAuth 2.0](https://www.1password.dev/users-api/authorization.md)
* Read the [API conventions](https://www.1password.dev/users-api/conventions.md) to learn how to authenticate, structure requests, and interpret responses and errors.
* [Get started with 1Password Security Automation](https://support.1password.com/security-automation/) to see integrations built by 1Password partners.

## Reference

Use the [API reference](https://www.1password.dev/users-api/reference.md) to learn more about each endpoint.

### OAuth tokens

* [Request an access token](https://www.1password.dev/users-api/request-access-token.md)
* [Introspect an access token](https://www.1password.dev/users-api/introspect-access-token.md)
* [Revoke an access token](https://www.1password.dev/users-api/revoke-access-token.md)

### Users

* [List users](https://www.1password.dev/users-api/list-users.md)
* [Get a user](https://www.1password.dev/users-api/get-user.md)
* [Suspend a user](https://www.1password.dev/users-api/suspend-user.md)
* [Reactivate a user](https://www.1password.dev/users-api/reactivate-user.md)

## Provide feedback

To provide feedback, or if you're interested in partnering with 1Password as an integrated service, you can [contact 1Password](mailto:msp@1password.com). We'd love to hear from you.


## Related topics

- [Get data and analytics for your 1Password account](https://www.1password.dev/get-started/get-data-and-analytics.md)
- [Programmatically manage your 1Password organization](https://www.1password.dev/get-started/manage-organization.md)
- [1Password Users API reference](https://www.1password.dev/users-api/reference.md)

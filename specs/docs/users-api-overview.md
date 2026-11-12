> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Users API

> Connect your 1Password account to a security automation platform with the 1Password Users API.

Use the 1Password Users API to list, look up, suspend, and reactivate people in your 1Password account from your own security tools. It's a REST API that uses OAuth 2.0 for authorization. Teams use it to:

* Enforce 1Password access rules automatically.
* Suspend or reactivate a user when your SIEM or the [Events API](https://www.1password.dev/events-api.md) flags something.
* Respond to security incidents faster, with fewer manual steps.

> [card] [Try the API in your browser](https://www.1password.dev/users-api/list-users.md)
>
> Paste your token and send a real request from the docs. No code needed.

## Requirements

The Users API requires you to have:

* A [1Password Business](https://1password.com/business-security) or [1Password Enterprise Password Manager](https://1password.com/product/enterprise-password-manager) account (not included with 1Password Teams)
* Permissions to manage integrations in your 1Password account (for example, as an [owner](https://support.1password.com/groups/#owners), [administrator](https://support.1password.com/groups/#administrators), or member of the [Security group](https://support.1password.com/groups/#security))

> [!WARNING]
>
> Integrations with the Users API are not currently supported if you use [automated provisioning](https://support.1password.com/provisioning/) with your 1Password account. You can manage actions, like suspending and reactivating users, through your identity provider.

## Quickstart

[Start here](https://www.1password.dev/users-api/get-started.md) to set up an OAuth integration and send your first authentication request to the Users API.

## Try it in the docs

Every endpoint in the [API reference](https://www.1password.dev/users-api/reference.md) has a **Try it** panel. Paste an access token and send a real request from your browser. No code needed.

**1. Get an access token**

Follow [Get started](https://www.1password.dev/users-api/get-started.md) to set up the Users API, then use [Request an access token](https://www.1password.dev/users-api/request-access-token.md) to get a token.

**2. Start with a read-only call**

Open [List users](https://www.1password.dev/users-api/list-users.md). It reads the people in your account without changing anything.

**3. Paste your token**

Select **Try it**, then paste your access token in the **Authorization** field.

**4. Send it, then copy the code**

Select **Send** to see the live response. The example panel turns the same request into cURL, Python, JavaScript, Go, and more.

> [!WARNING]
>
> Suspend and reactivate calls change real accounts. Try them on a test user first.

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

- [Get started with the 1Password Users API](https://www.1password.dev/users-api/get-started.md)
- [Authorize your integrations using OAuth 2.0](https://www.1password.dev/users-api/authorization.md)
- [1Password Users API reference](https://www.1password.dev/users-api/reference.md)

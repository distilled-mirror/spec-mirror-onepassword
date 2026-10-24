> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# About the 1Password Events API

The 1Password Events API is a REST-style API that follows the [OpenAPI 3.0 Specifications](https://spec.openapis.org/oas/latest.html). All communication between clients and servers are over HTTPS.

You can use your preferred language and tools for testing and implementing the Events API. The examples in the [API reference](https://www.1password.dev/events-api/reference.md) use [curl ](https://curl.se/) on the command line to demonstrate example requests. You can replace the values in any request with your own to receive information about events in your 1Password account.

The API can access data from the last 120 days. If you need to access data from more than 120 days ago, you can [use the audit log in your 1Password account](https://support.1password.com/activity-log/).

## Requirements

Before you can use the 1Password Events API, you'll need to:

* [Sign up for 1Password Business](https://1password.com/pricing/password-manager).
* [Set up an Events Reporting integration](https://www.1password.dev/events-api/get-started.md) in your 1Password account.
* [Create a bearer token](https://www.1password.dev/events-api/authorization.md#step-1-get-a-bearer-token) and select the event features it can access.


## Related topics

- [1Password Events API](https://www.1password.dev/events-api.md)
- [Get started with the 1Password Events API](https://www.1password.dev/events-api/get-started.md)
- [About the Events API beta](https://www.1password.dev/events-api/beta/about-v3-beta.md)

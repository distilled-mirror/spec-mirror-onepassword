> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# 1Password Connect

1Password Connect Servers allow you to securely access your 1Password items and vaults in your company's apps and cloud infrastructure using a private REST API.

Because Connect servers cache your data in your infrastructure, they allow unlimited re-requests after the server fetches your secrets.

The only request quotas that apply to Connect servers are the internal rate limits 1Password employs to keep our services stable and available. These only apply when a Connect server fetches secrets for the first time, like when the Connect server starts.

You can integrate a Connect server into your infrastructure and communicate with it over HTTP using one of the Connect SDK libraries (such as [Go](https://github.com/1Password/connect-sdk-go), [Python](https://github.com/1Password/connect-sdk-python), or [JavaScript](https://github.com/1Password/connect-sdk-js)) or using a custom integration.

> [!TIP]
>
> **Not sure if Connect servers are for you?**
>
> See the [Secrets Automation comparison table](/secrets-automation.md#comparison).

![The Connect server is part of your environment, and communicates to Your Apps using access tokens and a REST API.](https://mintcdn.com/ab-634991b8/P8nNS8jHoY1I7R9r/static/img/connect-diagram.png?fit=max\&auto=format\&n=P8nNS8jHoY1I7R9r\&q=85\&s=70b961d4ea5fa816b3da9b73907f09eb)

## Use cases

You can use 1Password Connect to accomplish a variety of tasks:

Availability

### Reduce latency and downtime

Deploy a Connect server in your infrastructure, giving you complete control. Self-hosting also reduces latency and has security benefits, as only your services can interact with Connect.

You can also deploy redundant Connect servers to further increase availability.

CI/CD pipelines

### Load secrets into CI/CD pipelines

Use a Connect server to automatically access your secrets in continuous integration environments.

You can use a Connect server to use 1Password items in your CI/CD pipeline for testing and deploying code. Doing so maintains security and avoids using sensitive information with a personal account.

[Learn more](/ci-cd.md)

Web services

### Provision web services with secrets

Use a Connect server to provision an account with a secret stored in 1Password.

If a web service needs access to a database (and the credentials for the database are in 1Password), you can use a Connect server to provision an account with the needed secret and allow the web service to access the secret during test runs.

Infrastructure secrets

### Secure infrastructure secrets

Use a Connect server to make sure infrastructure secrets aren't tied to a personal user account.

Secrets management

### Automate secrets management

Use a Connect server to automate scripts to manage secrets.

Using a Connect server helps you implement the principle of least privilege and avoid the limitations of personal accounts (for example, SSO and MFA requirements).

[Learn more](/connect/manage-connect.md)

Development workflows

### Streamline development workflows

Use a Connect server to securely share and manage infrastructure secrets to streamline development workflows.

Integration

### Secure Kubernetes environments

Use a Connect server to access and sync 1Password secrets in a Kubernetes environment.

[Learn more](/k8s/integrations.md)

## Get started

Learn how to get started and how to create, manage, and integrate with Connect servers.

Get started


## Related topics

- [connect](/cli/reference/management-commands/connect.md)
- [Connect concepts](/connect/concepts.md)
- [Manage Connect servers](/connect/manage-connect.md)

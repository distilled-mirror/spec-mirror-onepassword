> ## Documentation Index
> Fetch the complete documentation index at: https://www.1password.dev/llms.txt
> Use this file to discover all available pages before exploring further.

# Get started with a 1Password Connect server

export const YouTubeVideo = ({src, title, alt}) => {
  return <Frame>
      <iframe className="w-full aspect-video rounded-xl" src={src} title={alt ?? title} allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen></iframe>
    </Frame>;
};

export const ImageButton = ({src, darkSrc, url, text, height, alt}) => <a className="image-button-link inline-block mr-4 mb-2 no-underline !border-0 !border-b-0 !shadow-none text-[var(--op-secure-blue)] dark:text-[var(--op-cornflower)] hover:no-underline hover:!border-0 hover:!border-b-0 hover:!shadow-none group" href={url}>
    <div className="flex items-center gap-2">
      {darkSrc ? <>
          <span className="dark:hidden">
            <Icon icon={src} size={32} />
          </span>
          <span className="hidden dark:inline">
            <Icon icon={darkSrc} size={32} />
          </span>
        </> : <Icon icon={src} size={32} />}
      <span className="font-medium text-[1.1rem] group-hover:underline group-hover:decoration-[var(--op-secure-blue)] dark:group-hover:decoration-[var(--op-cornflower)] group-hover:underline-offset-4">
        {text}
      </span>
    </div>
  </a>;

export const Small = ({children}) => {
  return <small>{children}</small>;
};

1Password Connect servers are a type of [Secrets Automation workflow](/secrets-automation/) that allows you to securely access your 1Password items and vaults in your company's apps and cloud infrastructure.

<YouTubeVideo src="https://www.youtube.com/embed/PMwxZxZT2Pc" title="Getting Started with 1Password Connect" />

## Requirements

Before you can create a 1Password Secrets Automation workflow as a Connect server, make sure you complete the prerequisite tasks. The tasks vary depending on how you plan to deploy.

<Tabs queryString="deploy" groupId="requirements">
  <Tab title="Docker">
    * [Sign up for a 1Password account](https://1password.com/pricing/password-manager).
    * [Create a vault](https://support.1password.com/create-share-vaults/) for the Connect server to access. Connect servers can't access your built-in [Personal](https://support.1password.com/1password-glossary/#personal-vault), [Private](https://support.1password.com/1password-glossary/#private-vault), or [Employee](https://support.1password.com/1password-glossary/#employee-vault) vault, or your default [Shared](https://support.1password.com/1password-glossary/#shared-vault) vault.
    * Make sure you belong to [a group with permission to manage Secrets Automation](/connect/manage-connect/).
    * Make sure you have a deployment environment for Docker. You can use a cloud service provider or your local machine.
  </Tab>

  <Tab title="Kubernetes">
    * [Sign up for a 1Password account](https://1password.com/pricing/password-manager).
    * [Create a vault](https://support.1password.com/create-share-vaults/) for the Connect server to access. Connect servers can't access your built-in [Personal](https://support.1password.com/1password-glossary/#personal-vault), [Private](https://support.1password.com/1password-glossary/#private-vault), or [Employee](https://support.1password.com/1password-glossary/#employee-vault) vault, or your default [Shared](https://support.1password.com/1password-glossary/#shared-vault) vault.
    * Make sure you belong to [a group with permission to manage Secrets Automation](/connect/manage-connect/).
    * Make sure you have a deployment environment for Kubernetes with [kubectl <Icon icon="arrow-up-right-from-square" />](https://kubernetes.io/docs/tasks/tools/install-kubectl/), and [Helm <Icon icon="arrow-up-right-from-square" />](https://helm.sh/) installed. You can use a cloud service provider or your local machine.
  </Tab>
</Tabs>

<Card title="New to 1Password developer tools?" href="/get-started/developer-quickstart" icon="book-open">
  Follow the developer quickstart to set up the CLI, secure your secrets, and add biometric SSH signing in one guide.
</Card>

## Deployment

Use the following instructions to deploy a 1Password Connect Server.

<h3 id="step-1">
  Step 1: Create a Secrets Automation workflow
</h3>

You can create a Connect server Secrets Automation workflow through the 1Password.com dashboard or 1Password CLI. Following these instructions creates:

* A `1password-credentials.json` file. It contains the credentials necessary to deploy 1Password Connect Server.
* An access token. Use this in your applications or services to authenticate with the [Connect REST API](/connect/api-reference/). You can [issue additional tokens later](/connect/manage-connect/#create-a-token).

<Tabs queryString="method" groupId="1password-method">
  <Tab title="1Password.com">
    1. [Sign in](https://start.1password.com/signin) to your account on 1Password.com.
    2. Open the [Secrets Automation workflow creation wizard](https://start.1password.com/developer-tools/infrastructure-secrets/connect).<br /> <Small>Or, navigate to **Developer** > **Directory**, select **Other** under Infrastructure Secrets Management, then select **Create a Connect server**.</Small>
    3. Follow the onscreen instructions to create a Secrets Automation environment, set up your first access token, and deploy a Connect server. Make sure to save your credentials file and token in 1Password.

    You can find your new Secrets Automation workflow under "Connect servers" on the [**Developer**](https://start.1password.com/developer-tools/active) page.
  </Tab>

  <Tab title="1Password CLI">
    You can use the [1Password CLI](https://1password.com/downloads/command-line) [`op connect` command](/cli/reference/management-commands/connect/) to set up a Secrets Automation workflow with a Connect server.

    1. Make sure you have the latest version of [1Password CLI](/cli/get-started/) on your machine.

    2. Switch to the directory where you want to create the `1password-credentials.json`.

       Creating a Connect server automatically generates the `1password-credentials.json` file in the current directory. This file contains the credentials you'll need to deploy the Connect server. If a `1password-credentials.json` file already exists in the current directory, 1Password CLI will ask if you want to overwrite it.

    3. Create a Connect server and grant it access to a shared vault using the [`op connect server create` command](/cli/reference/management-commands/connect/#connect-server-create).

       ```shell theme={null}
       op connect server create <serverName> --vaults <vaultName>
       ```

    <Tip>
      Omit the `--vaults` flag to create a Connect server without granting it access to a vault. You can grant the Connect server access to shared vaults later using the [`op connect vault grant` command](/cli/reference/management-commands/connect/#connect-vault-grant).
    </Tip>

    If the Connect server or vault name contains one or more spaces, enclose the name in quotation marks (for example, `“My Server Name”`). You don't need to enclose strings in quotation marks if they don't contain spaces (for example, `myServerName`).

    ```
    op connect server create "My Server Name" --vault "My Vault Name"
    ```

    4. Create a token for the Connect server using the [`op connect token create` command](/cli/reference/management-commands/connect/#connect-token).

       ```shell theme={null}
       op connect token create <tokenName> --server <serverName|serverID> --vault <vaultName|vaultID>
       ```

    <Tip>
      Looking up a Connect server by its ID is more efficient than using the Connect server's name. See [Unique identifiers (IDs)](/cli/reference/#unique-identifiers-ids).

      You can find the ID of a Connect server by running [`op connect server list`](/cli/reference/management-commands/connect/#connect-server-list).
    </Tip>

    If successful, 1Password CLI returns a token string that you can use in your applications or services to authenticate with the [Connect server REST API](/connect/api-reference/). You can [issue additional tokens later.](/connect/manage-connect/#create-a-token)

    5. Save the token in 1Password so you don't lose it.

       You can have multiple tokens for the same Connect server but each Connect server has its own distinct set of tokens. See the [`op connect token` command reference](/cli/reference/management-commands/connect/#connect-token).
  </Tab>
</Tabs>

<Tip>
  Export your Connect server access token as an environment variable. Doing so might prove useful if you decide to configure another tool like the [Kubernetes Injector](/k8s/injector/), [Kubernetes Operator](/k8s/operator/), or other integrations. However, keep in mind that the recommended way to use the Connect server token with Kubernetes is as a Kubernetes Secret.

  ```shell theme={null}
  export OP_CONNECT_TOKEN=<token>
  ```
</Tip>

<h3 id="step-2-deploy-1password-connect-server">
  Step 2: Deploy a 1Password Connect Server
</h3>

<Tabs queryString="deploy" groupId="deploy">
  <Tab title="Docker">
    To deploy a Connect server using a Docker Compose file, you'll need to start two Docker images:

    * [1password/connect-api <Icon icon="arrow-up-right-from-square" />](https://hub.docker.com/r/1password/connect-api) serves the Connect server REST API.
    * [1password/connect-sync <Icon icon="arrow-up-right-from-square" />](https://hub.docker.com/r/1password/connect-sync) keeps the information available on the Connect server in sync with 1Password.com.

    <Note>
      If you aren't familiar with Docker or Docker Compose, refer to the [Docker Compose documentation <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/compose/) for more information.
    </Note>

    1. Make sure you have [Docker <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/engine/install/) and [Docker Compose <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/compose/install/) on your machine.

    2. Create a Docker Compose file to deploy the 1Password Connect Server containers in the directory where you saved your `1password-credentials.json` file. You can also use the example [`docker-compose.yaml` file](https://i.1password.com/media/1password-connect/docker-compose.yaml).

       If the credentials file is in the other directory, update the `volumes` section to point to the correct credentials file location.

           <Tip>
             You can set any of the [Connect server environment variables](/connect/server-configuration/) in the `docker-compose.yaml` file by adding an environment attribute to each container. Doing so lets you specify things like the `1password-credentials.json` file location, the log level, and the HTTP port. Refer to the [Docker environment attribute documentation <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/compose/environment-variables/set-environment-variables/) for more information.
           </Tip>

    3. Make sure Docker is running. You can [check if the Docker daemon is running <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/config/daemon/troubleshoot/#check-whether-docker-is-running) with the `docker info` command in a terminal application.

    4. Start the Docker containers with Docker Compose. Run the following command in the directory with the `docker-compose.yaml` file.

       ```shell theme={null}
       docker compose up
       ```

           <Tip>
             You can run the containers in the background using the `-detach` flag or the `-d` flag. Refer to the [`docker compose up` reference <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/engine/reference/commandline/compose_up/) for more information.
           </Tip>

       By default, you can access the Connect REST API through port `8080` on the local host. Refer to the [1Password Connect server API reference](/connect/api-reference/) for more information.

    #### Test the Connect server REST API

    You can make sure the [Connect server REST API](/connect/api-reference/) is accessible using a [`curl` <Icon icon="arrow-up-right-from-square" />](https://curl.se/docs/manpage.html) command, one of the 1Password Connect server API endpoints, and the token you created.

    1. Export the Connect server access token as an environment variable. This is the same token you created in [Step 1](#step-1). Alternatively, you can replace `$OP_API_TOKEN` with the token string in the `curl` request.

           <Tip>
             If you forgot your token, you can create another one with the [`op connect token create` command](/cli/reference/management-commands/connect/#connect-token-create).
           </Tip>

       ```shell theme={null}
       export OP_API_TOKEN="<token>"
       ```

    2. Use the following example [`curl` <Icon icon="arrow-up-right-from-square" />](https://curl.se/docs/manpage.html) command to list the vaults connected to the Connect server.

       ```shell theme={null}
       curl \
           -H "Accept: application/json" \
           -H "Authorization: Bearer $OP_API_TOKEN" \
           http://localhost:8080/v1/vaults
       ```

    To stop the Docker containers, run `docker compose down`.
  </Tab>

  <Tab title="Kubernetes">
    You can deploy a Connect server with Kubernetes. The easiest way to do this is with the [1Password Connect and Operator Helm chart](/k8s/helm/).

    <Warning>
      Before you start, you must have a Kubernetes cluster deployed. If you don't already have a cluster, you can create one locally using [minikube <Icon icon="arrow-up-right-from-square" />](https://minikube.sigs.k8s.io/docs/start/) or use the [Play with Kubernetes <Icon icon="arrow-up-right-from-square" />](https://labs.play-with-k8s.com/) playground.
    </Warning>

    1. Make sure you have [Docker <Icon icon="arrow-up-right-from-square" />](https://docs.docker.com/engine/install/) installed and running on your machine.

    2. Install the latest [Helm <Icon icon="arrow-up-right-from-square" />](https://helm.sh) release.

    3. Add the 1Password Helm chart repository.

       The following command adds the [1Password Helm chart repository](/k8s/helm/) to your local instance of Helm. This allows you to download and install all the charts from 1Password's GitHub repository.

       ```shell theme={null}
       helm repo add 1password https://1password.github.io/connect-helm-charts/
       ```

    4. Install the 1Password Connect server using Helm.

       The following command deploys the 1Password Connect server using the `1password-credentials.json` file.

    <Tip>
      You can find the `1password-credentials.json` file in the directory where you created the Connect server.
    </Tip>

    ```shell theme={null}
    helm install connect 1password/connect --set-file connect.credentials=1password-credentials.json
    ```

    #### Other ways to deploy

    Here are some ways you can deploy a Connect server on a Kubernetes cluster:

    ##### Deploy without Helm

    You can deploy a Connect server on a Kubernetes cluster without using Helm charts. See the [sample Kubernetes deployment <Icon icon="github" />](https://github.com/1Password/connect/tree/main/examples/kubernetes) on 1Password's GitHub that uses [cert-manager](https://cert-manager.io/) to provision a TLS (transport layer security) certificate for an external domain.

    <h5 id="with-operator">
      Deploy alongside the Kubernetes Operator
    </h5>

    The 1Password Connect Kubernetes Operator integrates [Kubernetes Secrets <Icon icon="arrow-up-right-from-square" />](https://kubernetes.io/docs/concepts/configuration/secret/) with 1Password. It also auto-restarts deployments when 1Password items are updated. [Learn more about the Kubernetes Operator](/k8s/operator/).
  </Tab>
</Tabs>

### Step 3: Set up applications and services to get information from 1Password

Applications and services get information from 1Password through REST API requests to a Connect server. The requests are authenticated with an access token. [Create a new token](/connect/manage-connect/#create-a-token) for each application or service you use.

<CardGroup cols={2}>
  <div>
    #### Languages

    <ImageButton alt="Go" src="/static/img/logos/golang.svg" height="32px" url="https://github.com/1Password/connect-sdk-go" text="Go" />

    <br />

    <ImageButton alt="Node.js" src="/static/img/logos/nodejs.svg" height="32px" url="https://github.com/1Password/connect-sdk-js" text="Node.js" />

    <br />

    <ImageButton alt="Python" src="/static/img/logos/python.svg" height="32px" url="https://github.com/1Password/connect-sdk-python" text="Python" />
  </div>

  <div>
    #### Plugins

    <ImageButton alt="Terraform provider" src="/static/img/logos/terraform.svg" height="32px" url="https://github.com/1Password/terraform-provider-onepassword" text="Terraform provider" />

    <br />

    <ImageButton alt="Kubernetes integrations" src="/static/img/logos/kubernetes.svg" height="32px" url="/k8s/integrations" text="Kubernetes integrations" />

    <br />

    <ImageButton alt="Hashicorp Vault backend" src="/static/img/logos/vault.svg" height="32px" url="https://github.com/1Password/vault-plugin-secrets-onepassword" text="Hashicorp Vault backend" />

    <br />

    <ImageButton alt="Ansible collection" src="/static/img/logos/ansible.svg" height="32px" url="/connect/ansible-collection" text="Ansible collection" />
  </div>
</CardGroup>

If your language or platform isn't listed, you can [build your own client using the 1Password Connect Server REST API](/connect/api-reference/).

You can also [use 1Password CLI](/connect/cli/) with your Connect server to provision secrets and retrieve item information on the command line.

## Get help

To change the vaults a token has access to, [issue a new token](/connect/manage-connect/#create-a-token).

To get help and share feedback, join the discussion with the [1Password Support Community](https://1password.community/categories/secrets-automation/).


## Related topics

- [Use CI/CD integrations with a 1Password Connect server](/connect/ci-cd.md)
- [Use 1Password CLI with a Connect server](/connect/cli.md)
- [1Password Connect Server API reference](/connect/api-reference.md)

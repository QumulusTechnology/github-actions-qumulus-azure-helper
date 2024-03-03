# GitHub Actions Qumulus Azure Helper

- [GitHub Actions Qumulus Azure Helper](github-actions-qumulus-azure-helper)
  - [Input Parameters](#input-parameters)
    - [`environment`](#environment)
    - [`access`](#access)
    - [`secrets`](#secrets)
    - [`min_mask_length`](#min_mask_length)
  - [Workflow Examples](#workflow-examples)
    - [Login to Qumulus Azure AD Subscription](#login-to-azure-ad-subscription)
    - [Fetch Azure KeyVault Secrets](#fetch-azure-keyvaults-secrets)
    - [Login to Qumulus Azure AD Subscription and fetch secrets](#login-to-azure-ad-subscription-and-fetch-secrets)

## Input Parameters

|Parameter Name|Required?|Type|Default Value|Description|
|---|---|---|---|---|
|environment|false|string|development|whether to access the production or development secrets|
|access|false|string|keyvault|whether to access just `keyvault` secrets or the Azure `subscription`|
|secrets|false|string||list of secrets required to fetch seperated by newline - output1:my-secret1 |
|min_mask_length|false|number||Minimum line length for a secret to be masked|

### `environment`

The input parameter `environment` specifies the environment github actions is running in.

It defaults to "development" but if "production" is specified than it will access the production subscription

### `access`

The input parameter `access` specifies whether you need access to the Azure subsciption for further tasks.

If "keyvault" is specified than access is specific to KeyVault to fetch secrets and then logged out.

if "subscription" is specified, than access is given to the Azure subscription

It's better to create a GitHub Action secret for this parameter when using it. Refer to [Using secrets in GitHub Actions](https://docs.github.com/actions/security-guides/using-secrets-in-github-actions).

### `secrets`

The input parameter `secrets` specifies list of secrets to access
and inject into the environment. Due to limitations with GitHub
Actions inputs, this is specified as a string.

You can specify multiple secrets by putting each secret on its own line:

```yaml
secrets: |-
  output1:my-secret1
  output2:my-secret2
```

### `min_mask_length`

(Optional, default: "4") Minimum line length for a secret
  to be masked. Extremely short secrets (e.g. "{" or "a") can make GitHub
  Actions log output unreadable. This is especially important for multi-line
  secrets, since each line of the secret is masked independently.

## Outputs

Each secret is prefixed with an output name. The secret's resolved access value
will be available at that output in future build steps.

For example:

```yaml
jobs:
  job_id:
    steps:
    - id: 'secrets'
      uses: 'QumulusTechnology/github-actions-get-keyvault-secrets@v1'
      with:
        environment: "production"
        access: "keyvault"
        secrets: |-
          secret_1/secret-1
```

will be available in future steps as the output "token":

```yaml
# other step
- id: 'publish'
  uses: 'foo/bar@v1'
  env:
    TOKEN: '${{ steps.secrets.outputs.secret_1 }}'
```

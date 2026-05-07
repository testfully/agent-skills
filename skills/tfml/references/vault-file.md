# Vault File

Vault files are detected only when inside a `vaults/` directory. Secret values
are never persisted in TFML files; only metadata is stored.

## Fields

| Field              | Type            | Required | Description                                                                         |
| ------------------ | --------------- | -------- | ----------------------------------------------------------------------------------- |
| `type`             | `string`        | No       | `aws`, `azure`, `local`, `os`, or `testfully`.                                      |
| `name`             | `string`        | No       | Optional vault name. If missing, filename without extension is used.                |
| `description`      | `string`        | No       | Vault description.                                                                  |
| `external_id`      | `string`        | No       | External identifier.                                                                |
| `enabled`          | `boolean`       | No       | Whether the vault is enabled. Defaults to enabled when omitted.                     |
| `pull_all_secrets` | `boolean`       | No       | Whether Testfully should pull all provider secrets. Defaults to false when omitted. |
| `domains`          | `string`        | No       | Comma-separated domains where this vault is available. Omitted means all.           |
| `secret`           | `VaultSecret[]` | No       | Secret metadata. Values are not stored.                                             |
| `aws_profile`      | `string`        | No       | AWS profile for AWS Secrets Manager.                                                |
| `system_secrets`   | `boolean`       | No       | Use the operating system native secret manager when true.                           |

## Provider Inference

`type` maps to provider as follows:

| `type`      | Provider                 |
| ----------- | ------------------------ |
| `aws`       | AWS Secrets Manager      |
| `azure`     | Azure Vault              |
| `local`     | Local encrypted vault    |
| `os`        | OS-native secret manager |
| `testfully` | Testfully vault          |

`aws_profile` implies AWS provider. `system_secrets = true` implies OS-native
provider.

## VaultSecret

| Field         | Type       | Required | Description                                                      |
| ------------- | ---------- | -------- | ---------------------------------------------------------------- |
| `name`        | `string`   | No       | Secret name.                                                     |
| `enabled`     | `boolean`  | No       | Whether the secret is enabled. Defaults to enabled when omitted. |
| `external_id` | `string`   | No       | External ID in the provider.                                     |
| `version`     | `string`   | No       | Secret version string.                                           |
| `domains`     | `string[]` | No       | Domains where this secret is available.                          |

## Example

```toml
type = "aws"
name = "Production secrets"
description = "Secrets loaded from AWS"
enabled = true
pull_all_secrets = false
domains = "api.example.com,admin.example.com"
aws_profile = "prod"

[[secret]]
name = "api-token"
external_id = "prod/api-token"
version = "latest"
enabled = true
domains = ["api.example.com"]
```

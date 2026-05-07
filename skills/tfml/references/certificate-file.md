# Certificate File

Certificate files are detected only when inside a `certificates/` directory.
They are manifests that reference companion certificate/key files by relative
path or filename.

## Fields

| Field        | Type      | Required | Description                                                                        |
| ------------ | --------- | -------- | ---------------------------------------------------------------------------------- |
| `enabled`    | `boolean` | No       | Enabled flag. Defaults to enabled when omitted.                                    |
| `hosts`      | `string`  | No       | Comma-separated hostnames. Client certificates only.                               |
| `ca`         | `string`  | No       | Certificate authority file. When set, the manifest is treated as a CA certificate. |
| `cert`       | `string`  | No       | Client certificate file.                                                           |
| `key`        | `string`  | No       | Private key file.                                                                  |
| `pfx`        | `string`  | No       | PFX file.                                                                          |
| `passphrase` | `string`  | No       | Private key or PFX passphrase.                                                     |

Do not author any additional fields; they will be ignored.

## Authoring Rules

- Use `ca` for CA certificates.
- Use `cert` plus `key` for PEM client certificates.
- Use `pfx` for PFX client certificates.
- Use `hosts` for client certificates to scope hostname matching.

## Examples

```toml
ca = "root-ca.pem"
```

```toml
hosts = "api.example.com,admin.example.com"
cert = "client.pem"
key = "client-key.pem"
passphrase = "{{certPassphrase}}"
```

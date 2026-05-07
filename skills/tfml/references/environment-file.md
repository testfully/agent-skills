# Environment File

Environment files represent Testfully environments. An environment is a
collection of variables and settings that can be applied to requests.

- Testfully Desktop inspects the `environments` directly under the project root
  for environment files. Environment files in other locations will be ignored.

- Testfully CLI processes environment files in the `environments` directory, and
  also when explicitly supplied via a flag or argument to commands that accept
  them.

## Fields

Environment files support the following fields.

| Field       | Type                | Required | Description                                                                        |
| ----------- | ------------------- | -------- | ---------------------------------------------------------------------------------- |
| `name`      | `string`            | No       | Optional environment name. If missing, filename without extension is used.         |
| `type`      | `string`            | No       | `internet`, `local`, or `private`. Defaults to `internet` when invalid or omitted. |
| `history`   | `string`            | No       | `store`, `do_not_store`, `store_session`, or `not_set`.                            |
| `timeout`   | `number`            | No       | Timeout in milliseconds. Omit or set to `0`/`null` for default.                    |
| `redirects` | `boolean \| number` | No       | Redirect behavior.                                                                 |
| `secure`    | `boolean`           | No       | SSL verification flag.                                                             |
| `insecure`  | `boolean`           | No       | Inverse SSL verification flag.                                                     |
| `variables` | `Variable[]`        | No       | Environment variables.                                                             |

Furthermore, environment files also accept any of the fields supported by
following objects:

- [Authorization](./authorization.md)

## Example

Below is an example of an environment file that defines a "Development"
environment with a `baseUrl` variable.

```toml
name = "Development"

[variables]
baseUrl = "https://httpbin.org"
```

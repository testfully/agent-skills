# Common Objects

These objects are shared by multiple TFML schemas.

## Header

Used by folders and request steps.

| Field     | Type      | Required | Description                                                      |
| --------- | --------- | -------- | ---------------------------------------------------------------- |
| `name`    | `string`  | No       | Header name.                                                     |
| `value`   | `string`  | No       | Header value.                                                    |
| `enabled` | `boolean` | No       | Whether the header is enabled. Defaults to enabled when omitted. |

## Param

Used by folders and request steps.

| Field     | Type      | Required | Description                                                         |
| --------- | --------- | -------- | ------------------------------------------------------------------- |
| `name`    | `string`  | No       | Query parameter name.                                               |
| `value`   | `string`  | No       | Query parameter value.                                              |
| `enabled` | `boolean` | No       | Whether the parameter is enabled. Defaults to enabled when omitted. |

## Variable

Used by globals, folders, and environments.

| Field     | Type      | Required | Description                                                        |
| --------- | --------- | -------- | ------------------------------------------------------------------ |
| `name`    | `string`  | No       | Variable name.                                                     |
| `value`   | `string`  | No       | Variable value.                                                    |
| `enabled` | `boolean` | No       | Whether the variable is enabled. Defaults to enabled when omitted. |

### How to use

Reference variables in supported string fields with the `{{variable_name}}`
format. For example, a variable named `baseUrl` can be used as `{{baseUrl}}`.

## SSL Verification

Used by folders, request steps, and environments.

| Field      | Type      | Required | Description                                                                                      |
| ---------- | --------- | -------- | ------------------------------------------------------------------------------------------------ |
| `secure`   | `boolean` | No       | `true` means verify SSL; `false` means do not verify SSL. When unset, inherit/default to verify. |
| `insecure` | `boolean` | No       | `true` means do not verify SSL; `false` means verify SSL. When unset, inherit/default to verify. |

If both fields are set, `secure` takes precedence. Prefer writing only one.

## Redirects

Used by folders, request steps, and environments.

| Value                  | Meaning                                      |
| ---------------------- | -------------------------------------------- |
| omitted or `null`      | Inherit/default behavior.                    |
| `false`                | Do not follow redirects.                     |
| `true`                 | Follow redirects with the default max limit. |
| positive number        | Follow redirects up to that number.          |
| `0` or negative number | Do not follow redirects.                     |

## History

Used by folders, request steps, and environments.

Allowed lowercase values:

```text
store
do_not_store
store_session
not_set
```

## HTTP Methods

Allowed lowercase request methods:

```text
get
post
put
patch
delete
head
options
```

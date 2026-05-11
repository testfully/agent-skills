# Request File

In context of Testfully, a request represents an HTTP request to a server. Use a
request file and the specification in this reference to author metadata for a
request or multi-step scenario.

- Testfully Desktop looks for request files inside the `collections` directory.
  Files outside the `collections` directory are ignored entirely.

- Testfully CLI looks for request files in the `collections` directory, and also
  accepts requests via arguments for commands that support them.

## Supported Request Fields

Below is a list of supported fields for request files.

### `name` field

Use the `name` field (optional) to specify a human-friendly name for the
request. We suggest using filenames to name requests instead of `name` field,
but if you have a specific need for a separate name, you can use this field.
When omitted, Testfully uses the filename (without extension) as the request
name.

To define a request called "Hello, World!", you can create a file named
`hello-world.toml` with the following content:

```toml
name = "Hello, World!"
```

### `method` field

HTTP requests have a method that indicates the desired action to be performed on
the resource. Use the `method` field to specify the HTTP method for the request.
When omitted or invalid, the method defaults to `get`.

Valid HTTP methods include `get`, `post`, `put`, `patch`, `delete`, `options`,
and `head`.

For example, to send a `POST` request to `https://httpbin.org/anything`
endpoint, we would do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"
```

### `url` field

The `url` field is the main field as it specifies the endpoint to which the
request will be sent.

For example, to send a request to `https://httpbin.org/anything`, we would do
the following:

```toml
url = "https://httpbin.org/anything"
```

You can use variables in the URL field, and they will be resolved at runtime.
For example, if you have a variable named `baseUrl` defined in the same folder
or inherited from a parent folder or environment, you can use it in the URL like
this:

```toml
url = "${baseUrl}/anything"
```

### `headers` field

Use the `[headers]` section (optional) to specify one or more HTTP headers to
include with the request. Each header is a key-value pair when the key is the
header name and the value is the header value.

For example, to set the `Content-Type` header to `application/json` and an
`Authorization` header with a bearer token, we would do the following:

```toml
[headers]
Content-Type = "application/json"
Authorization = "Bearer ${accessToken}"
```

If I wanted to disable the `Authorization` header without deleting it, I could
prefix it with `-` like this:

```toml
[headers]
Content-Type = "application/json"
-Authorization = "Bearer ${accessToken}"
```

This way the `Authorization` header is ignored when sending the request, but I
can easily re-enable it later by removing the `-` prefix.

### `params` field

Use the `[params]` section (optional) to specify one or more query parameters to
include with the request. Each parameter is a key-value pair where the key is
the parameter name and the value is the parameter value.

For example, to set a `search` query parameter to `testfully` and a `version`
query parameter to `2026-05`, we would do the following:

```toml
[params]
search = "testfully"
version = "2026-05"
```

Continuing with the example, if I wanted to disable the `search` query parameter
without deleting it, I could prefix it with `-` like this:

```toml
[params]
-search = "testfully"
version = "2026-05"
```

If I wanted to have a configurable `version` parameter that I can easily change
or disable, I could use a variable for it like this:

```toml
url = "https://httpbin.org/anything"
[params]
search = "testfully"
version = "${version}"
```

### `cookies` field

Use the `[cookies]` section (optional) to specify one or more cookies to include
with the request. Each cookie is a key-value pair where the key is the cookie
name and the value is the cookie value.

For example, to send a `session_id` cookie with a value resolved from a
variable, we would do the following:

```toml
[cookies]
session_id = "${sessionId}"
```

If I wanted to disable the `session_id` cookie without deleting it, I could
prefix it with `-` like this:

```toml
[cookies]
-session_id = "${sessionId}"
```

This way the `session_id` cookie is ignored when sending the request, but I can
easily re-enable it later by removing the `-` prefix.

### `json` field

Use the `json` field (optional) when you want to send a request with a JSON
request payload. This is useful for most API create and update requests, such as
sending a customer record to a `POST` endpoint. Because JSON payloads often span
multiple lines, prefer TOML multiline strings with `"""`.

```toml
method = "post"
url = "https://httpbin.org/anything"
json = """
{
  "name": "Ada Lovelace",
  "active": true
}
"""
```

### `xml` field

Use the `xml` field (optional) when you want to send a request with an XML
request payload. For example, to submit a small XML document to an endpoint, we
would use a TOML multiline string with `"""` and do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"
xml = """
<user>
  <name>Ada Lovelace</name>
</user>
"""
```

### `javascript` field

Use the `javascript` field (optional) when you want to send a request with a
JavaScript request payload. For example, to submit a short script body, we would
do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"
javascript = """
export default {
  enabled: true
}
"""
```

### `html` field

Use the `html` field (optional) when you want to send a request with an HTML
request payload. For example, to send a rendered snippet or template body, we
would use a TOML multiline string with `"""` and do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"
html = """
<main>
  <h1>Hello, World!</h1>
</main>
"""
```

### `text` field

Use the `text` field (optional) when you want to send a request with a plain
text request payload. For example, to send a simple note or message body, we
would do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"
text = "Hello, World!"
```

### `raw` field

Use the `raw` field (optional) when you want to send a request with a raw
request payload that does not fit one of the more specific payload fields. For
example, to preserve a custom payload format, we would do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"
raw = "status=ready;priority=high"
```

### `yaml` field

Use the `yaml` field (optional) when you want to send a request with a YAML
request payload. For example, to send configuration as YAML, we would do the
following:

```toml
method = "post"
url = "https://httpbin.org/anything"
yaml = """
name: Ada Lovelace
enabled: true
"""
```

### `graphql_query` field

Use the `graphql_query` field (optional) when you want to send a request with a
GraphQL request payload. For example, to fetch a user by ID, we would do the
following:

```toml
method = "post"
url = "https://httpbin.org/anything"
graphql_query = """
query ($id: ID!) {
  user(id: $id) {
    name
    email
  }
}
"""
```

### `graphql_variables` field

Use the `graphql_variables` field (optional) to pass variables for a GraphQL
operation. It is commonly used with `graphql_query`, such as passing the `id`
variable used by the query:

```toml
graphql_query = """
query ($id: ID!) {
  user(id: $id) {
    name
    email
  }
}
"""
graphql_variables = """
{
  "id": "123"
}
"""
```

### `form` field

Use the `form` field (optional) when you want to send a request with a
`multipart/form-data` request payload. Each form field is defined using a
`[[form]]` section. For example, to upload an avatar file alongside a display
name, we would do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"

[[form]]
type = "text"
name = "display_name"
value = "Ada"

[[form]]
type = "file"
name = "avatar"
value = "./avatar.png"
content_type = "image/png"
```

Each `[[form]]` entry accepts the following fields:

| Field          | Type      | Required | Description                                                                                           |
| -------------- | --------- | -------- | ----------------------------------------------------------------------------------------------------- |
| `type`         | `string`  | No       | `text` or `file`. Defaults to `text` when invalid or omitted.                                         |
| `name`         | `string`  | No       | Field name.                                                                                           |
| `value`        | `string`  | No       | For text fields, this will be the field value. For file fields, this should point to the actual file. |
| `enabled`      | `boolean` | No       | Whether the field is enabled. Defaults to enabled.                                                    |
| `download_url` | `string`  | No       | Download URL for file fields.                                                                         |
| `content_type` | `string`  | No       | Content type for file fields.                                                                         |

### `url_encoded` field

Use the `url_encoded` field (optional) when you want to send a request with an
`application/x-www-form-urlencoded` request payload. Each field is defined using
a `[[url_encoded]]` section. For example, to send OAuth token request fields, we
would do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"

[[url_encoded]]
name = "grant_type"
value = "client_credentials"

[[url_encoded]]
name = "client_id"
value = "${clientId}"
```

Each `[[url_encoded]]` entry accepts the following fields:

| Field     | Type      | Required | Description                                        |
| --------- | --------- | -------- | -------------------------------------------------- |
| `name`    | `string`  | No       | Field name.                                        |
| `value`   | `string`  | No       | Field value.                                       |
| `enabled` | `boolean` | No       | Whether the field is enabled. Defaults to enabled. |

### `binary` field

Use the `binary` field (optional) when you want to send a request with a binary
request payload. For example, to send a ZIP file as the request payload, we
would do the following:

```toml
method = "post"
url = "https://httpbin.org/anything"

[binary]
filename = "./payload.zip"
```

The `[binary]` section accepts the following fields:

| Field      | Type     | Required |
| ---------- | -------- | -------- |
| `filename` | `string` | No       |

### Payload Type Priority

When importing a step, Testfully infers request payload type from fields in this
priority:

```text
json
form
url_encoded
binary
xml
javascript
html
text
raw
yaml
graphql_query/graphql_variables
```

Avoid setting more than one payload shape unless intentionally preserving
existing data.

### `validation` field

Use the `validation` field (optional) to store expected response results with
the request. Each validation is defined using a `[[validation]]` section. For
example, to expect a `201` response within 500 milliseconds, we would do the
following:

```toml
[[validation]]
code = 201
time = 500
```

Each `[[validation]]` entry accepts the following fields:

| Field            | Type                                  | Required | Description                        |
| ---------------- | ------------------------------------- | -------- | ---------------------------------- |
| `environment_id` | `string`                              | No       | Environment ID for the validation. |
| `time`           | `number`                              | No       | Response time expectation.         |
| `code`           | `number`                              | No       | Expected response status code.     |
| `body`           | `string`                              | No       | Expected response body.            |
| `headers`        | `{ name?: string; value?: string }[]` | No       | Expected response headers.         |

### `secure` field

Use the `secure` field (optional) to control SSL certificate verification. When
omitted, Testfully uses inherited or default SSL behavior. For example, to skip
verification when testing a server with a self-signed certificate, we would do
the following:

```toml
secure = false
```

### `insecure` field

Use the `insecure` field (optional) as the inverse SSL verification flag. When
set to `true`, SSL verification is skipped. If both `secure` and `insecure` are
set, `secure` takes precedence.

```toml
insecure = true
```

### `redirects` field

Use the `redirects` field (optional) to control redirect handling. Set it to
`false` to stop following redirects, `true` to use the default redirect limit,
or a positive number to set a maximum redirect count. For example, to follow up
to five redirects, we would do the following:

```toml
redirects = 5
```

### `history` field

Use the `history` field (optional) to control request history storage. Valid
values include `store`, `do_not_store`, `store_session`, and `not_set`. For
example, to avoid storing a sensitive request in history, we would do the
following:

```toml
history = "do_not_store"
```

### `pre_request` field

Use the `pre_request` field (optional) to run a script before the request is
sent. This is useful when you need to generate a timestamp, sign the request, or
add a header before the request is sent.

```toml
pre_request = """
const timestamp = Date.now();
$.request.headers.upsert({
  key: "X-Request-Timestamp",
  value: String(timestamp),
});
"""
```

### `post_response` field

Use the `post_response` field (optional) to run a script after the response is
received. This is useful when you need to extract a value from the response and
reuse it in a later request.

```toml
post_response = """
const data = $.response.json();

$.environment.set("lastResponseUrl", data.url);
"""
```

### `allocated_memory` field

Use the `allocated_memory` field (optional) to set how much memory Testfully
allocates to sandbox scripts for a request step. This is useful when
`pre_request` or `post_response` scripts need more memory than the default.

The value should be a number in megabytes. When omitted, Testfully allocates
`1`.

```toml
allocated_memory = 64
```

### `timeout` field

Use the `timeout` field (optional) to limit how long the request may run, in
milliseconds. When omitted or set to `null`, Testfully uses the default timeout.
For example, to set a 10-second timeout, we would do the following:

```toml
timeout = 10000
```

## Multi-step requests

Testfully supports a concept multi-step requests, where a request file can
contain multiple HTTP requests that are executed in sequence. Each step of a
multi-step request is defined using a `[[request]]` section.

For example, to define a multi-step request that first creates a user and then
fetches the user details, we would do the following:

```toml
[[request]]
name = "Create User"
method = "post"
url = "https://httpbin.org/anything"
json = """
{
  "name": "Ada Lovelace"
}
"""

[[request]]
name = "Get User"
method = "get"
url = "https://httpbin.org/anything"
```

Each `[[request]]` entry accepts the same fields as a single-step request, such
as `method`, `url`, `headers`, etc., as documented in the previous sections. The
only additional field is `name`, which is used to identify each step within the
multi-step request.

## Authorization

To set an authorization scheme (e.g. OAuth2, AWS Signature V4) for a request,
you can use any of the supported authorization types documented in the
[Authorization reference](./authorization.md).

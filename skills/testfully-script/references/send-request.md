# Send HTTP Requests

Use `$.sendRequest()` to send a bespoke HTTP request from a Testfully script.
This is useful when a Before Request or After Response script needs to call a
helper endpoint, fetch a token, seed test data, or inspect another endpoint
without changing the request currently being executed.

## APIs

| API                       | Description                                     |
| ------------------------- | ----------------------------------------------- |
| `$.sendRequest(request)`  | Preferred Testfully API.                        |
| `pm.sendRequest(request)` | Postman-compatible alias for `$.sendRequest()`. |
| `sendRequest(request)`    | Legacy global alias for `$.sendRequest()`.      |

Prefer `$.sendRequest()` for new Testfully scripts. Use `pm.sendRequest()` when
porting Postman scripts with minimal changes.

All aliases share the same Testfully behavior: they return the response object.
Do not use Postman's callback form, because the sandbox implementation does not
call a second callback argument.

## Accepted Inputs

`sendRequest` accepts one argument: either a URL string or a request object.

When the input is a string, Testfully sends a `GET` request to that URL:

```javascript
const response = $.sendRequest("https://httpbin.org/anything");
console.log(response.code, response.body);
```

When the input is an object, the supported fields are:

| Field             | Required | Description                                         |
| ----------------- | -------- | --------------------------------------------------- |
| `url`             | Yes      | Request URL.                                        |
| `method`          | No       | HTTP method. Defaults to `GET`.                     |
| `headers`         | No       | Plain object of request headers.                    |
| `header`          | No       | Alias for `headers`; checked before `headers`.      |
| `timeout`         | No       | Timeout in seconds. `0` uses the 30-second default. |
| `body.mode`       | For body | One of `raw`, `graphql`, `formdata`, `urlencoded`.  |
| `body.raw`        | Raw      | String payload for `raw` mode.                      |
| `body.query`      | GraphQL  | GraphQL query string.                               |
| `body.variables`  | GraphQL  | GraphQL variables as an object or string.           |
| `body.formdata`   | Form     | Array of `{ key, value }` form fields.              |
| `body.urlencoded` | Form     | Array of `{ key, value }` URL-encoded fields.       |

For `raw` bodies, pass a string. Use `JSON.stringify()` when sending JSON.

## Request Object Fields

### `url`

The `url` field is required for request objects and defines the endpoint
Testfully should call. Include the full URL, including the protocol, host, path,
and any query string values that are part of the helper request.

```javascript
const response = $.sendRequest({
  url: "https://httpbin.org/anything?source=testfully",
});
```

### `method`

The `method` field controls the HTTP method used for the request. If omitted,
Testfully sends a `GET` request. Use standard HTTP methods such as `GET`,
`POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, or `OPTIONS`.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
});
```

### `headers`

The `headers` field provides request headers as a plain object. Use it for
content negotiation, authentication, custom tracing headers, or content type
declarations.

```javascript
const response = $.sendRequest({
  url: "https://httpbin.org/anything",
  headers: {
    Accept: "application/json",
    "X-Request-Source": "testfully-script",
  },
});
```

### `header`

The `header` field is an alias for `headers`. The sandbox checks `header` before
`headers`, so do not set both unless you intentionally want `header` to take
precedence. Prefer `headers` for new scripts because it is clearer.

```javascript
const response = $.sendRequest({
  url: "https://httpbin.org/anything",
  header: {
    "X-Request-Source": "legacy-script",
  },
});
```

### `timeout`

The `timeout` field sets the helper request timeout in seconds. Testfully's
default request timeout is 30 seconds. Omit `timeout` or set it to `0` to use
that 30-second default. Negative values are treated as `0`.

```javascript
const response = $.sendRequest({
  url: "https://httpbin.org/delay/1",
  timeout: 2,
});
```

### `body`

The `body` field describes the request payload. If `body` is present,
`body.mode` is required and must be one of `raw`, `graphql`, `formdata`, or
`urlencoded`. Omit `body` for requests without a payload.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "raw",
    raw: "hello from Testfully",
  },
});
```

### `body.mode`

The `body.mode` field tells Testfully how to interpret the payload. Supported
values are `raw`, `graphql`, `formdata`, and `urlencoded`.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "raw",
    raw: JSON.stringify({ name: "Testfully" }),
  },
});
```

### `body.raw`

The `body.raw` field provides the payload for `raw` mode. It must be a string.
When sending JSON, set an appropriate `Content-Type` header and pass
`JSON.stringify()` output.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  headers: {
    "Content-Type": "application/json",
  },
  body: {
    mode: "raw",
    raw: JSON.stringify({ active: true }),
  },
});
```

### `body.query`

The `body.query` field provides the GraphQL query string for `graphql` mode. It
is required when `body.mode` is `graphql`.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "graphql",
    query: "query Example { viewer { id } }",
  },
});
```

### `body.variables`

The `body.variables` field provides GraphQL variables for `graphql` mode. It can
be an object or a string. Objects are serialized by the sandbox before the
request is sent.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "graphql",
    query: "query Example($id: ID!) { node(id: $id) { id } }",
    variables: {
      id: "123",
    },
  },
});
```

### `body.formdata`

The `body.formdata` field provides form fields for `formdata` mode as an array
of `{ key, value }` entries. The current sandbox maps these entries to text form
fields.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "formdata",
    formdata: [
      { key: "name", value: "Testfully" },
      { key: "kind", value: "script" },
    ],
  },
});
```

### `body.urlencoded`

The `body.urlencoded` field provides URL-encoded fields for `urlencoded` mode as
an array of `{ key, value }` entries. Use it for OAuth-style token requests or
form endpoints that expect URL-encoded payloads.

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "urlencoded",
    urlencoded: [
      { key: "grant_type", value: "client_credentials" },
      { key: "scope", value: "read:users" },
    ],
  },
});
```

## Response

The returned response object contains:

| Field    | Description                          |
| -------- | ------------------------------------ |
| `code`   | HTTP status code, such as `200`.     |
| `status` | HTTP status text.                    |
| `body`   | Response body as a string.           |
| `text`   | Same response body string as `body`. |

Use `JSON.parse(response.body)` when the response body is JSON.

## Examples

Send a `GET` request:

```javascript
const response = $.sendRequest("https://httpbin.org/anything");
const data = JSON.parse(response.body);

console.log("Status:", response.code);
console.log("URL:", data.url);
```

Send a JSON `POST` request:

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  headers: {
    "Content-Type": "application/json",
  },
  body: {
    mode: "raw",
    raw: JSON.stringify({
      name: "Testfully",
      source: "script",
    }),
  },
});

const data = JSON.parse(response.body);
console.log("Posted JSON:", data.json);
```

Send a request with a timeout:

```javascript
const response = $.sendRequest({
  method: "GET",
  url: "https://httpbin.org/delay/1",
  timeout: 2,
});

console.log("Completed with:", response.code);
```

Use the Postman-compatible alias:

```javascript
const response = pm.sendRequest("https://httpbin.org/anything");
console.log("Postman-compatible status:", response.code);
```

Migrate callback-style Postman scripts to returned-response style:

```javascript
// Postman callback style is not used in Testfully scripts:
// pm.sendRequest("https://httpbin.org/anything", function (err, response) {});

const response = pm.sendRequest("https://httpbin.org/anything");
console.log("Migrated status:", response.code);
```

Send a GraphQL-style request body:

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "graphql",
    query: "query Example { viewer { id } }",
    variables: {
      example: true,
    },
  },
});

console.log("GraphQL payload echo:", response.body);
```

Send URL-encoded fields:

```javascript
const response = $.sendRequest({
  method: "POST",
  url: "https://httpbin.org/anything",
  body: {
    mode: "urlencoded",
    urlencoded: [
      { key: "grant_type", value: "client_credentials" },
      { key: "scope", value: "read:users" },
    ],
  },
});

console.log("Form response:", response.code);
```

## Guidance

- Prefer `$.sendRequest()` for new scripts and `pm.sendRequest()` for Postman
  migration compatibility.
- Keep helper requests focused. If the request is part of the main workflow,
  prefer modeling it as a Testfully request or chained request.
- Parse JSON responses explicitly with `JSON.parse(response.body)`.
- Use `timeout` for slow helper endpoints. Set `timeout: 0` or omit it to use
  Testfully's 30-second default request timeout.

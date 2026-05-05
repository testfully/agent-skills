# Current Request

Use `$.request` to inspect the outgoing request from a Before Request or After
Response script. In a Before Request script, you can also use the header helper
methods to change the headers that will be sent with the request.

## APIs

| API                                        | Description                          |
| ------------------------------------------ | ------------------------------------ |
| `$.request`                                | Preferred Testfully request API.     |
| `pm.request`                               | Postman-compatible alias.            |
| `$.request.method`                         | Current request HTTP method.         |
| `$.request.url`                            | Current request URL.                 |
| `$.request.type`                           | Current request body type.           |
| `$.request.headers`                        | Current request headers.             |
| `$.request.headers.add({ key, value })`    | Append a request header.             |
| `$.request.headers.upsert({ key, value })` | Add or update a request header.      |
| `$.request.headers.remove(key)`            | Remove a request header by name.     |
| `$.request.params`                         | Current request query parameters.    |
| `$.request.body`                           | Current request body object.         |
| `$.request.body.json`                      | JSON request payload string.         |
| `$.request.body.query`                     | GraphQL query string.                |
| `$.request.body.variables`                 | GraphQL variables string.            |
| `$.request.body.form`                      | Form fields for form-style requests. |
| `$.request.skip()`                         | Skip the request in Before Request.  |
| `pm.execution.skipRequest()`               | Postman-compatible skip alias.       |

Prefer `$.request` for new Testfully scripts. Use `pm.request` when migrating
Postman scripts.

## Request Shape

### `$.request`

`$.request` is the current request object available in the sandbox. It is a
snapshot of the request Testfully is about to send, or has already sent in an
After Response script.

Use `$.request` to read request details. To change outgoing headers, use the
header helper methods documented below.

```javascript
console.log("Request:", $.request.method, $.request.url);
```

### `pm.request`

`pm.request` is a Postman-compatible alias for `$.request`. It points to the
same request object in the sandbox.

```javascript
console.log("Postman-compatible URL:", pm.request.url);
```

### `$.request.method`

`$.request.method` is the HTTP method of the outgoing request, such as `GET`,
`POST`, `PUT`, `PATCH`, or `DELETE`.

```javascript
if ($.request.method === "POST") {
  console.log("Posting to:", $.request.url);
}
```

### `$.request.url`

`$.request.url` is the outgoing request URL. For Before Request scripts, the
sandbox source notes that this can be the uninterpolated URL, so variable
placeholders may still be visible depending on where the script runs in the
request lifecycle.

```javascript
console.log("Outgoing URL:", $.request.url);
```

### `$.request.type`

`$.request.type` is the request body type. Use it to branch body-handling logic
for JSON, GraphQL, form, URL-encoded, or empty requests.

```javascript
if ($.request.type === "JSON") {
  console.log("JSON payload:", $.request.body.json);
}
```

### `$.request.headers`

`$.request.headers` is an array of current request headers. Each header object
has `name` and `value` fields.

Use the array for reading and searching headers. To change outgoing headers, use
`add`, `upsert`, or `remove`; direct array mutation is not the supported way to
update the request.

```javascript
const authHeader = $.request.headers.find(
  (header) => header.name.toLowerCase() === "authorization",
);

console.log("Has auth header:", Boolean(authHeader));
```

### `$.request.headers.add({ key, value })`

`add` appends a new request header. The argument must be an object with `key`
and `value` fields. The method ignores calls without a `key`.

Use `add` when duplicate header names are intentional.

```javascript
$.request.headers.add({
  key: "X-Debug-Source",
  value: "testfully-script",
});
```

### `$.request.headers.upsert({ key, value })`

`upsert` updates the first existing header whose name matches `key`, ignoring
case. If no matching header exists, it appends a new one.

Use `upsert` for headers that should only appear once, such as `Authorization`,
`Content-Type`, or trace identifiers.

```javascript
$.request.headers.upsert({
  key: "Authorization",
  value: "Bearer " + $.environment.get("access_token"),
});
```

### `$.request.headers.remove(key)`

`remove` deletes headers whose name matches `key`, ignoring case. Use it to
strip stale headers before the request is sent.

```javascript
$.request.headers.remove("X-Debug-Source");
```

### `$.request.params`

`$.request.params` is an array of current query parameters. Each parameter
object has `name` and `value` fields.

The current sandbox exposes params for reading. It does not expose params
mutation helpers on `$.request.params`.

```javascript
const source = $.request.params.find((param) => param.name === "source");
console.log("source param:", source ? source.value : "not set");
```

### `$.request.body`

`$.request.body` is an object containing the body fields that match the request
type. It can include `json`, `query`, `variables`, and `form`.

Use `$.request.type` before reading a specific body field when the same script
may run on requests with different body types.

```javascript
console.log("Request body type:", $.request.type);
```

### `$.request.body.json`

`$.request.body.json` contains the JSON request payload string for JSON
requests. Parse it with `JSON.parse()` before reading object fields.

```javascript
if ($.request.body.json) {
  const payload = JSON.parse($.request.body.json);
  console.log("Payload keys:", Object.keys(payload));
}
```

### `$.request.body.query`

`$.request.body.query` contains the GraphQL query string for GraphQL requests.
Use it for diagnostics or to validate that a required operation is present.

```javascript
if ($.request.body.query && !$.request.body.query.includes("mutation")) {
  console.warn("Expected a mutation request");
}
```

### `$.request.body.variables`

`$.request.body.variables` contains GraphQL variables as a string. Parse it when
the value is JSON and the script needs to inspect individual variables.

```javascript
if ($.request.body.variables) {
  const variables = JSON.parse($.request.body.variables);
  console.log("GraphQL variable names:", Object.keys(variables));
}
```

### `$.request.body.form`

`$.request.body.form` is an array of form fields for form-data and URL-encoded
requests. Each field object has `name` and `value` fields.

```javascript
const username = $.request.body.form.find((field) => field.name === "username");
console.log("Has username field:", Boolean(username));
```

### `$.request.skip()`

`$.request.skip()` skips execution of the current request. It is available for
Before Request scripts. If it is called from an After Response script, Testfully
logs a warning and does not skip the request.

`pm.execution.skipRequest()` is the Postman-compatible alias and points to the
same function.

```javascript
if ($.environment.get("skip_current_request") === "true") {
  $.request.skip();
}
```

## Examples

### Add a Trace Header

```javascript
$.request.headers.upsert({
  key: "X-Trace-ID",
  value: $.uuid(),
});
```

### Inspect a Request to `httpbin.org`

```javascript
console.log("Method:", $.request.method);
console.log("URL:", $.request.url);

$.request.headers.upsert({
  key: "X-Testfully-Example",
  value: "httpbin-anything",
});
```

This pattern works well for a request pointed at `https://httpbin.org/anything`,
because that endpoint accepts common HTTP methods and echoes request details.

### Read a JSON Payload

```javascript
if ($.request.body.json) {
  const payload = JSON.parse($.request.body.json);
  console.log("Outgoing payload:", payload);
}
```

### Skip a Request Conditionally

```javascript
const shouldRun = $.environment.get("run_optional_request");

if (shouldRun !== "true") {
  console.warn("Skipping optional request:", $.request.url);
  $.request.skip();
}
```

## Guidance

- Use `$.request` for new scripts and `pm.request` for Postman migration
  compatibility.
- Treat `$.request` scalar fields and arrays as read-only snapshots except for
  the documented header helper methods.
- Use `$.request.headers.upsert()` when a header should have one effective
  value.
- Use `$.request.skip()` only in Before Request scripts.

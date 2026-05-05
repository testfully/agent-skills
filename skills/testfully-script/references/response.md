# Current Response

Use `$.response` to inspect the response returned by the current request. This
API is intended for After Response scripts, where Testfully has already received
the response and can expose status, headers, cookies, body, timing, and size
metadata.

## APIs

| API                       | Description                          |
| ------------------------- | ------------------------------------ |
| `$.response`              | Preferred Testfully response API.    |
| `pm.response`             | Postman-compatible alias.            |
| `$.response.code`         | HTTP response status code.           |
| `$.response.status`       | HTTP response status text.           |
| `$.response.headers`      | Response headers.                    |
| `$.response.body`         | Response body string.                |
| `$.response.cookies`      | Response cookies.                    |
| `$.response.text()`       | Return the response body string.     |
| `$.response.json()`       | Parse the response body as JSON.     |
| `$.response.responseTime` | Response duration in milliseconds.   |
| `$.response.responseSize` | Response size reported by Testfully. |

Prefer `$.response` for new Testfully scripts. Use `pm.response` when migrating
Postman scripts.

## Response Shape

### `$.response`

`$.response` is the current response object available in the sandbox. Use it in
After Response scripts to inspect the result of the request that just completed.

```javascript
console.log("Response:", $.response.code, $.response.status);
```

### `pm.response`

`pm.response` is a Postman-compatible alias for `$.response`. It points to the
same response object in the sandbox.

```javascript
console.log("Postman-compatible status:", pm.response.code);
```

### `$.response.code`

`$.response.code` is the numeric HTTP status code, such as `200`, `201`, `400`,
or `500`. Use it for status-based branching or assertions.

```javascript
if ($.response.code >= 400) {
  console.warn("Request failed:", $.response.code);
}
```

### `$.response.status`

`$.response.status` is the HTTP status text reported by Testfully for the status
code. Common values include `OK`, `CREATED`, `BAD REQUEST`, `UNAUTHORIZED`,
`FORBIDDEN`, `NOT FOUND`, `TOO MANY REQUESTS`, and `INTERNAL SERVER ERROR`.

```javascript
console.log("Status:", $.response.status);
```

### `$.response.headers`

`$.response.headers` is an array of response headers. Each header object has
`name` and `value` fields.

Use the array for reading and searching response headers. Header names are best
compared case-insensitively.

```javascript
const contentType = $.response.headers.find(
  (header) => header.name.toLowerCase() === "content-type",
);

console.log("Content-Type:", contentType ? contentType.value : "not set");
```

### `$.response.body`

`$.response.body` is the response body as a string. Use it directly for text
responses, or parse it when the response contains structured data.

```javascript
console.log("Raw body:", $.response.body);
```

### `$.response.cookies`

`$.response.cookies` is an array of cookies returned by the response. Each
cookie object has `name` and `value` fields.

Cookie names are case-sensitive.

```javascript
const sessionCookie = $.response.cookies.find(
  (cookie) => cookie.name === "session",
);

console.log("Has session cookie:", Boolean(sessionCookie));
```

### `$.response.text()`

`$.response.text()` returns the response body as a string. It is equivalent to
reading `$.response.body`.

```javascript
const body = $.response.text();
console.log("Body length:", body.length);
```

### `$.response.json()`

`$.response.json()` parses the response body with `JSON.parse()` and returns the
parsed value. It throws an error when the response body is not valid JSON.

Use it when the current response is expected to be JSON. If the response might
not be JSON, catch parsing errors or inspect the `Content-Type` header first.

```javascript
const data = $.response.json();
console.log("JSON keys:", Object.keys(data));
```

### `$.response.responseTime`

`$.response.responseTime` is the request duration in milliseconds. Use it for
performance assertions, diagnostics, or logs.

```javascript
if ($.response.responseTime > 1000) {
  console.warn("Slow response:", $.response.responseTime + "ms");
}
```

### `$.response.responseSize`

`$.response.responseSize` is the response size reported by Testfully. Use it for
diagnostics or size-based checks.

```javascript
console.log("Response size:", $.response.responseSize);
```

## Examples

### Inspect a Response from `httpbin.org`

Point a request at `https://httpbin.org/anything`, then use this in an After
Response script:

```javascript
console.log("Status:", $.response.code);
console.log("Time:", $.response.responseTime + "ms");

const data = $.response.json();
console.log("Echoed URL:", data.url);
```

### Assert a Successful JSON Response

```javascript
$.test("Response is successful JSON", function () {
  $.expect($.response.code).to.equal(200);

  const data = $.response.json();
  $.expect(data).to.be.an("object");
});
```

### Read a Header

```javascript
const contentType = $.response.headers.find(
  (header) => header.name.toLowerCase() === "content-type",
);

if (!contentType || !contentType.value.includes("application/json")) {
  console.warn("Expected JSON response");
}
```

### Handle Non-JSON Responses

```javascript
try {
  const data = $.response.json();
  console.log("Parsed response:", data);
} catch (error) {
  console.warn("Response was not JSON:", $.response.text());
}
```

### Use the Postman-Compatible Alias

```javascript
pm.test("Status is OK", function () {
  pm.expect(pm.response.code).to.equal(200);
});
```

## Guidance

- Use `$.response` for new scripts and `pm.response` for Postman migration
  compatibility.
- Use `$.response.json()` only when the response body is valid JSON.
- Compare header names case-insensitively.
- Use `$.response.cookies` for reading cookies returned by the current response.
- Keep response assertions in After Response scripts so the response data is
  meaningful.

# Multi-Step Requests

Use `$.steps(stepIndex)` to read request and response information from a
completed step in a multi-step request. This is useful when a later step needs
data produced by an earlier step, or when an After Response script needs to
validate data across multiple steps.

`$.steps()` is available in both Before Request and After Response scripts.

## APIs

| API                  | Description                                       |
| -------------------- | ------------------------------------------------- |
| `$.steps(stepIndex)` | Return a completed step by zero-based step index. |

## `$.steps(stepIndex)`

`$.steps(stepIndex)` returns an object with two top-level properties: `request`
and `response`.

The `stepIndex` is zero-based, so `$.steps(0)` returns the first completed step,
`$.steps(1)` returns the second completed step, and so on.

```javascript
const firstStep = $.steps(0);

console.log("First step method:", firstStep.request.method);
console.log("First step status:", firstStep.response.code);
```

Only completed steps can be accessed. If the index is missing, negative, or does
not point to an executed step, Testfully throws an error.

## Return Type

`$.steps(stepIndex)` returns this shape:

```typescript
{
  request: {
    method: string;
    url: string;
    dispatchedAt: string;
    headers: Array<{ name: string; value: string }>;
    params: Array<{ name: string; value: string }>;
    body: string | Array<{ name: string; value: string }>;
  }
  response: {
    receivedAt: string;
    code: number;
    body: string;
    headers: Array<{ name: string; value: string }>;
    cookies: Array<{ name: string; value: string }>;
    error: string;
  }
}
```

### `step.request.method`

The HTTP method used by the step request, such as `GET`, `POST`, `PUT`, or
`DELETE`.

```javascript
const step = $.steps(0);
console.log(step.request.method);
```

### `step.request.url`

The full request URL for the step, including query parameters.

```javascript
const step = $.steps(0);
console.log(step.request.url);
```

### `step.request.dispatchedAt`

An ISO timestamp for when Testfully sent the step request.

```javascript
const step = $.steps(0);
console.log("Sent at:", step.request.dispatchedAt);
```

### `step.request.headers`

An array of request header objects. Each header has `name` and `value` fields.

```javascript
const step = $.steps(0);
const authorization = step.request.headers.find(
  (header) => header.name.toLowerCase() === "authorization",
);
```

### `step.request.params`

An array of query parameter objects. Each parameter has `name` and `value`
fields.

```javascript
const step = $.steps(0);
const page = step.request.params.find((param) => param.name === "page");
```

### `step.request.body`

The request body sent by the step. For raw bodies and file bodies, the value is
a string. For form data and URL-encoded data, the value is an array of
`{ name, value }` objects.

```javascript
const step = $.steps(0);

if (Array.isArray(step.request.body)) {
  const token = step.request.body.find((field) => field.name === "token");
  console.log("Token field:", token ? token.value : "not set");
} else {
  console.log("Request body:", step.request.body);
}
```

### `step.response.receivedAt`

An ISO timestamp for when Testfully received the step response.

```javascript
const step = $.steps(0);
console.log("Received at:", step.response.receivedAt);
```

### `step.response.code`

The numeric HTTP status code returned by the step response.

```javascript
const step = $.steps(0);
console.log("Status:", step.response.code);
```

### `step.response.body`

The response body as a string. Parse it with `JSON.parse()` when the response is
JSON.

```javascript
const loginStep = $.steps(0);
const loginData = JSON.parse(loginStep.response.body);

console.log("User ID:", loginData.user.id);
```

### `step.response.headers`

An array of response header objects. Each header has `name` and `value` fields.

```javascript
const step = $.steps(0);
const contentType = step.response.headers.find(
  (header) => header.name.toLowerCase() === "content-type",
);
```

### `step.response.cookies`

An array of response cookie objects. Each cookie has `name` and `value` fields.

```javascript
const step = $.steps(0);
const session = step.response.cookies.find((cookie) => cookie.name === "sid");
```

### `step.response.error`

The error message for the step response. It is an empty string when the step did
not fail with an error.

```javascript
const step = $.steps(0);

if (step.response.error) {
  console.warn("Step failed:", step.response.error);
}
```

## Examples

### Use Data From a Previous Step

Use a value from the first step response in the current request.

```javascript
const loginStep = $.steps(0);
const loginData = JSON.parse(loginStep.response.body);

$.request.headers.upsert({
  key: "Authorization",
  value: `Bearer ${loginData.token}`,
});
```

### Validate Data Across Steps

Use `$.steps()` in an After Response script to compare data returned by earlier
steps.

```javascript
$.test("User ID is consistent across steps", function () {
  const firstStep = $.steps(0);
  const secondStep = $.steps(1);

  const firstUserId = JSON.parse(firstStep.response.body).user.id;
  const secondUserId = JSON.parse(secondStep.response.body).user.id;

  $.expect(firstUserId).to.equal(secondUserId);
});
```

### Debug Executed Steps

Loop over known step indexes and stop when a step is not available.

```javascript
for (let index = 0; index < 5; index += 1) {
  try {
    const step = $.steps(index);
    const duration =
      new Date(step.response.receivedAt) - new Date(step.request.dispatchedAt);

    console.log(`Step ${index}`);
    console.log("Method:", step.request.method);
    console.log("URL:", step.request.url);
    console.log("Status:", step.response.code);
    console.log("Duration:", duration + "ms");
  } catch (error) {
    break;
  }
}
```

## Error Behavior

`$.steps()` throws an error when:

- `stepIndex` is missing.
- `stepIndex` is negative.
- The requested step cannot be found.
- Testfully cannot retrieve the requested step.

Use `try`/`catch` when probing for optional steps.

```javascript
let firstStep;

try {
  firstStep = $.steps(0);
} catch (error) {
  console.warn("First step is not available:", error.message);
}
```

## Guidance

- Use zero-based indexes: `0` for the first step, `1` for the second step.
- Read only completed steps.
- Parse `step.response.body` yourself when the body contains JSON.
- Check whether `step.request.body` is an array before treating it as form data.
- Use `$.steps()` for cross-step validation, debugging, and propagating data
  from earlier steps into later requests.

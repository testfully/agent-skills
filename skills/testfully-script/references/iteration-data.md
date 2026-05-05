# Iteration Data

Use `$.iterationData` to read and update data for the current Collection Runner
iteration. Iteration data usually comes from a CSV or JSON data file uploaded
when running a collection, and lets the same collection execute multiple times
with different input values.

`$.iterationData` is available in Before Request and After Response scripts. It
also works when sending an individual request; when no data file is available,
it behaves like an empty data object.

Testfully also exposes `pm.iterationData` for Postman compatibility. Prefer
`$.iterationData` in new Testfully scripts.

## APIs

| API                                | Description                         |
| ---------------------------------- | ----------------------------------- |
| `$.iterationData.get(key)`         | Get an iteration data value.        |
| `$.iterationData.has(key)`         | Check whether a key exists.         |
| `$.iterationData.set(key, value)`  | Set an iteration data value.        |
| `$.iterationData.unset(key)`       | Remove an iteration data value.     |
| `$.iterationData.toObject()`       | Return iteration data as an object. |
| `$.iterationData.toJSON()`         | Return iteration data as JSON.      |
| `pm.iterationData.get(key)`        | Postman-compatible alias.           |
| `pm.iterationData.has(key)`        | Postman-compatible alias.           |
| `pm.iterationData.set(key, value)` | Postman-compatible alias.           |
| `pm.iterationData.unset(key)`      | Postman-compatible alias.           |
| `pm.iterationData.toObject()`      | Postman-compatible alias.           |
| `pm.iterationData.toJSON()`        | Postman-compatible alias.           |

## Core APIs

### `$.iterationData.get(key)`

`$.iterationData.get(key)` returns the value for `key` in the current iteration
data. If the key does not exist, it returns `undefined`.

```javascript
const userId = $.iterationData.get("user_id");

if (userId) {
  $.request.headers.upsert({
    key: "X-User-ID",
    value: String(userId),
  });
}
```

Values can be strings, numbers, booleans, arrays, objects, or `null`, depending
on the data file and values set by scripts.

### `$.iterationData.has(key)`

`$.iterationData.has(key)` returns `true` when the current iteration data
contains `key`, otherwise it returns `false`.

Use it when a value may intentionally be empty but the key still matters.

```javascript
if (!$.iterationData.has("email")) {
  console.warn("This iteration does not include an email value");
}
```

### `$.iterationData.set(key, value)`

`$.iterationData.set(key, value)` sets a value in the current iteration data.
The value is available to later reads from `$.iterationData` for the current
iteration.

```javascript
const email = $.iterationData.get("email");

if (email) {
  $.iterationData.set("email_domain", String(email).split("@")[1]);
}
```

Use `set()` for derived values that are useful across the current request's
Before Request and After Response logic.

```javascript
$.iterationData.set("started_at", new Date().toISOString());
```

### `$.iterationData.unset(key)`

`$.iterationData.unset(key)` removes a value from the current iteration data. If
the key does not exist, the call has no effect.

```javascript
$.iterationData.unset("temporary_token");
```

### `$.iterationData.toObject()`

`$.iterationData.toObject()` returns the entire current iteration data as an
object snapshot.

```javascript
const data = $.iterationData.toObject();
console.log("Iteration keys:", Object.keys(data));
```

Use the returned object for logging, branching, or passing data into helper
functions.

### `$.iterationData.toJSON()`

`$.iterationData.toJSON()` returns the entire current iteration data as a JSON
string.

```javascript
console.log("Iteration data:", $.iterationData.toJSON());
```

Use this when you need a serialized snapshot for logs or request bodies.

```javascript
$.request.headers.upsert({
  key: "X-Debug-Iteration",
  value: $.iterationData.toJSON(),
});
```

## Postman Compatibility

`pm.iterationData` exposes the same methods as `$.iterationData` and points to
the same current iteration data.

```javascript
const userId = pm.iterationData.get("user_id");

pm.test("Iteration includes a user ID", function () {
  pm.expect(userId).to.not.equal(undefined);
});
```

Prefer `$.iterationData` for new Testfully scripts because it is consistent with
other Testfully scripting APIs.

## Examples

### Build a Request from CSV Data

Given a CSV data file with `user_id` and `email` columns, use the values in a
Before Request script.

```javascript
const userId = $.iterationData.get("user_id");
const email = $.iterationData.get("email");

$.request.headers.upsert({
  key: "X-User-ID",
  value: String(userId),
});

$.request.headers.upsert({
  key: "X-User-Email",
  value: String(email),
});
```

### Validate the Expected Status Per Iteration

Given a data file with an `expected_status` column, assert each iteration's
expected response code in an After Response script.

```javascript
$.test("Response has the expected status", function () {
  const expectedStatus = Number($.iterationData.get("expected_status"));

  $.expect($.response.code).to.equal(expectedStatus);
});
```

### Derive a Value Once and Reuse It

Use a Before Request script to derive a value from iteration data.

```javascript
const email = $.iterationData.get("email");

if (email) {
  $.iterationData.set("email_domain", String(email).split("@")[1]);
}
```

Use the derived value later in the same script flow.

```javascript
const domain = $.iterationData.get("email_domain");

if (domain) {
  console.log("Testing user domain:", domain);
}
```

### Send Iteration Data in a Helper Request

Use `toJSON()` when a helper request should include the current iteration
payload as JSON.

```javascript
const response = $.sendRequest({
  url: "https://httpbin.org/anything",
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: {
    mode: "raw",
    raw: $.iterationData.toJSON(),
  },
});

console.log("Helper request status:", response.code);
```

### Handle Missing Data Safely

```javascript
if (!$.iterationData.has("access_token")) {
  console.warn("No access_token supplied for this iteration");
} else {
  $.request.headers.upsert({
    key: "Authorization",
    value: `Bearer ${$.iterationData.get("access_token")}`,
  });
}
```

## Guidance

- Use iteration data for values that change per Collection Runner iteration.
- Use `has()` when an empty string or `null` can be a meaningful value.
- Use `get()` for a single key and `toObject()` when a helper needs the whole
  data row.
- Use `set()` for derived or temporary values needed during the current
  iteration.
- Use `pm.iterationData` only when migrating Postman scripts.
- Do not use iteration data for workspace-wide or environment-specific values;
  use `$.globals`, `$.environment`, or folder and collection variables instead.

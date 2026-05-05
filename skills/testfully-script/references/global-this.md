# Globally Scoped Script Values

Use `globalThis` when you need JavaScript constants or helper functions to be
available to scripts within the same collection or folder tree. A common pattern
is to define shared helpers at the collection level, then call them from request
scripts inside that collection.

This is different from Testfully Global Variables. `$.globals` stores
workspace-level variable values through Testfully's variable system.
`globalThis` defines JavaScript values in the script runtime, such as booleans,
functions, parsers, and small shared configuration objects.

## APIs

| API                                     | Description                              |
| --------------------------------------- | ---------------------------------------- |
| `globalThis.NAME = value`               | Define a globally scoped value.          |
| `globalThis.helper = function (...) {}` | Define a globally scoped function.       |
| `NAME`                                  | Read a globally scoped value.            |
| `globalThis.NAME`                       | Read a globally scoped value explicitly. |
| `typeof NAME !== "undefined"`           | Check whether a scoped value exists.     |

## Defining Values

Define globally scoped values by assigning properties on `globalThis`.

```javascript
globalThis.SHOULD_LOG = true;
```

After a value is defined, scripts in the same accessible folder or collection
tree can read it directly.

```javascript
if (SHOULD_LOG) {
  console.log("Logging is enabled");
}
```

Use explicit `globalThis.NAME` reads when you want the dependency to be obvious.

```javascript
if (globalThis.SHOULD_LOG) {
  console.info("Request URL:", $.request.url);
}
```

## Scope Rules

Globally scoped script values follow Testfully's folder and collection
structure. A value defined on a collection or folder is available to scripts in
requests and sub-folders within that tree. Sibling folders and requests do not
share each other's scoped values.

Before Request and After Response scripts also have separate execution scopes. A
value defined only in a Before Request script is not automatically available in
an After Response script. Define shared values in both phases when both phases
need them.

```javascript
// Collection-level Before Request script
globalThis.SHOULD_LOG = true;

// Collection-level After Response script
globalThis.SHOULD_LOG = true;
```

## Practical Examples

### Toggle Shared Logging

Define `SHOULD_LOG` and a helper at collection level.

```javascript
globalThis.SHOULD_LOG = true;

globalThis.logDebug = function (...args) {
  if (globalThis.SHOULD_LOG) {
    console.log(...args);
  }
};
```

Use the helper in request scripts.

```javascript
logDebug("Sending request:", $.request.method, $.request.url);
```

Use the same pattern in After Response scripts.

```javascript
logDebug("Response:", $.response.code, $.response.responseTime + "ms");
```

### Shared JSON Parsing Helper

Define a helper that parses JSON and records useful context when parsing fails.

```javascript
globalThis.parseJson = function (text, label) {
  try {
    return JSON.parse(text);
  } catch (error) {
    throw new Error(`${label} is not valid JSON: ${error.message}`);
  }
};
```

Use it in an After Response script.

```javascript
$.test("Response contains a user ID", function () {
  const data = parseJson($.response.body, "Response body");

  $.expect(data.user.id).to.be.a("number");
});
```

### Shared Authorization Header Helper

Define a helper at collection level for requests that need a bearer token.

```javascript
globalThis.useBearerToken = function (token) {
  $.request.headers.upsert({
    key: "Authorization",
    value: `Bearer ${token}`,
  });
};
```

Use it from a request Before Request script.

```javascript
const token = $.environment.get("access_token");

if (token) {
  useBearerToken(token);
}
```

### Shared Response Assertion Helper

Define a small validation helper in the After Response scope.

```javascript
globalThis.expectJsonResponse = function () {
  $.expect($.response.code).to.equal(200);

  const contentType = $.response.headers.find(
    (header) => header.name.toLowerCase() === "content-type",
  );

  $.expect(contentType.value).to.include("application/json");
  return $.response.json();
};
```

Use it in request-level After Response scripts.

```javascript
$.test("Response is JSON", function () {
  const data = expectJsonResponse();

  $.expect(data).to.be.an("object");
});
```

### Guard Optional Helpers

When a request may run outside the collection or folder where a helper is
defined, guard access with `typeof`.

```javascript
if (typeof logDebug !== "undefined") {
  logDebug("Optional debug log");
}
```

## Guidance

- Use `globalThis` for JavaScript helpers, constants, and lightweight shared
  configuration.
- Use `$.globals`, `$.environment`, or folder and collection variables for
  values that should be stored in Testfully's variable system.
- Define helpers at the collection level when all requests in the collection
  need them.
- Define helpers at a folder level when only that folder tree should use them.
- Define values separately in Before Request and After Response scripts when
  both phases need access.
- Avoid storing secrets directly in `globalThis`; read secrets from environment
  or variable APIs instead.

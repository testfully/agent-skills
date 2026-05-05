# Postman Script Info

Use `pm.info` to read basic execution metadata from a Testfully script. This
object exists for Postman compatibility and is available in both Before Request
and After Response scripts.

Prefer Testfully's `$` APIs for Testfully-specific scripting. Use `pm.info` when
you are migrating Postman scripts or when you need phase, request, or iteration
metadata.

## APIs

| API                      | Description                       |
| ------------------------ | --------------------------------- |
| `pm.info`                | Script execution metadata object. |
| `pm.info.eventName`      | Current script phase.             |
| `pm.info.requestName`    | Current request name.             |
| `pm.info.requestId`      | Current request id.               |
| `pm.info.iteration`      | Current iteration number.         |
| `pm.info.iterationCount` | Total iteration count.            |

## Fields

### `pm.info`

`pm.info` is the Postman-compatible metadata object. It is attached to the `pm`
object and does not have a `$` alias.

```javascript
console.log("Script info:", pm.info);
```

### `pm.info.eventName`

`pm.info.eventName` identifies the phase where the script is running.

In the current sandbox implementation, Before Request scripts return
`prerequest` and After Response scripts return `test`.

```javascript
if (pm.info.eventName === "prerequest") {
  console.log("Before Request script:", pm.info.requestName);
}

if (pm.info.eventName === "test") {
  console.log("After Response script:", pm.info.requestName);
}
```

Use this when sharing a helper across both script phases and you need to guard
phase-specific APIs such as `$.response`.

```javascript
if (pm.info.eventName === "test" && $.response.code >= 400) {
  console.warn("Request failed:", $.response.code);
}
```

### `pm.info.requestName`

`pm.info.requestName` is the current request name. Use it for logs, assertions,
or shared helpers that should include request context.

```javascript
console.log("Running request:", pm.info.requestName);
```

### `pm.info.requestId`

`pm.info.requestId` is the current request id exposed through the
Postman-compatible info object. Use it when migrated scripts already reference
Postman request metadata.

```javascript
console.log("Request id:", pm.info.requestId);
```

For new flow-control scripts, use `$.setNextRequest(requestIdOrTitle)` when you
need to choose the next request in Collection Runner.

### `pm.info.iteration`

`pm.info.iteration` is the current iteration number. The value is numeric.

```javascript
console.log("Iteration:", pm.info.iteration);
```

In the current sandbox implementation, this value defaults to `1`.

### `pm.info.iterationCount`

`pm.info.iterationCount` is the total iteration count. The value is numeric.

```javascript
console.log("Iteration count:", pm.info.iterationCount);
```

In the current sandbox implementation, this value defaults to `1`.

## Examples

### Add Request Context to Logs

```javascript
console.log(
  `[${pm.info.eventName}] ${pm.info.requestName}`,
  "iteration",
  pm.info.iteration,
  "of",
  pm.info.iterationCount,
);
```

### Avoid Reading `$.response` in Before Request Scripts

Use `pm.info.eventName` before accessing response-only APIs from shared helper
code.

```javascript
globalThis.logPhaseSummary = function () {
  console.log("Request:", pm.info.requestName);

  if (pm.info.eventName === "test") {
    console.log("Status:", $.response.code);
  }
};

logPhaseSummary();
```

### Create a Stable Debug Prefix

```javascript
globalThis.debugPrefix = function () {
  return `[${pm.info.eventName}] ${pm.info.requestName}`;
};

console.log(debugPrefix(), "URL:", $.request.url);
```

### Skip Expensive Debug Logs Outside the First Iteration

```javascript
if (pm.info.iteration === 1) {
  console.log("Headers:", $.request.headers);
}
```

## Guidance

- Use `pm.info` for Postman compatibility and lightweight execution metadata.
- Treat `pm.info` as read-only metadata.
- Use `pm.info.eventName` to keep shared helpers safe across Before Request and
  After Response scripts.
- Do not use `pm.info` as a variable store; use `globalThis` for shared script
  helpers or Testfully variable APIs for persisted values.

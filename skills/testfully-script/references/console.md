# Console Logging

Use the global `console` object to write log entries from Testfully Before
Request and After Response scripts. Logs are shown in the request's Logs tab
(Desktop and Web) or terminal (CLI) and are useful for debugging script flow,
inspecting request or response values, and surfacing warnings or errors during
execution.

## APIs

| API                      | Log level | Description                         |
| ------------------------ | --------- | ----------------------------------- |
| `console.log(...args)`   | `info`    | Records an informational log entry. |
| `console.info(...args)`  | `info`    | Records an informational log entry. |
| `console.warn(...args)`  | `warn`    | Records a warning log entry.        |
| `console.error(...args)` | `error`   | Records an error log entry.         |
| `console.debug(...args)` | N/A       | Not supported in Testfully scripts. |
| `console.table(...args)` | N/A       | Not supported in Testfully scripts. |

## Behavior

- Each API accepts any number of arguments. Testfully records the arguments as
  the log entry payload.
- Log entries are timestamped by the sandbox when they are recorded.
- The console API is available in both Before Request and After Response script
  hooks.

## Examples

Log a simple message:

```javascript
console.log("Preparing request");
```

Inspect request and response values:

```javascript
console.info("Request URL:", $.request.url);
console.warn("Response status:", $.response.code);
```

Record an error-level message:

```javascript
if ($.response.code >= 500) {
  console.error("Server error response:", $.response.code, $.response.body);
}
```

## Guidance

- Use console logs for diagnostics and script observability. For API assertions,
  prefer Testfully's testing APIs such as `$.test()` and `expect()` so failures
  are reported as test results instead of plain logs.

# Global Variables

Use `$.globals` to work with Testfully global variables from scripts. Global
variables are workspace-level values that can be reused across requests in the
workspace.

Testfully also exposes Postman-compatible global variable APIs for migration,
but new scripts should prefer `$.globals`.

## APIs

| API                                      | Description                         |
| ---------------------------------------- | ----------------------------------- |
| `$.globals.get(name)`                    | Get a global variable value.        |
| `$.globals.has(name)`                    | Check whether a variable exists.    |
| `$.globals.set(name, value)`             | Set a global variable value.        |
| `$.globals.unset(name)`                  | Remove a global variable.           |
| `$.globals.clear()`                      | Remove all global variables.        |
| `$.globals.toObject()`                   | Return variables as a plain object. |
| `pm.globals.get(name)`                   | Postman-compatible alias.           |
| `pm.globals.has(name)`                   | Postman-compatible alias.           |
| `pm.globals.set(name, value)`            | Postman-compatible alias.           |
| `pm.globals.unset(name)`                 | Postman-compatible alias.           |
| `pm.globals.clear()`                     | Postman-compatible alias.           |
| `pm.globals.toObject()`                  | Postman-compatible alias.           |
| `postman.setGlobalVariable(name, value)` | Legacy Postman-compatible API.      |
| `postman.getGlobalVariable(name)`        | Legacy Postman-compatible API.      |
| `postman.clearGlobalVariable(name)`      | Legacy Postman-compatible API.      |
| `globals`                                | Legacy global variables object.     |

## Core APIs

### `$.globals.get(name)`

`$.globals.get(name)` returns the value of a global variable as a string. If the
variable does not exist, it returns `undefined`.

```javascript
const baseUrl = $.globals.get("base_url");

if (baseUrl) {
  console.log("Base URL:", baseUrl);
}
```

### `$.globals.has(name)`

`$.globals.has(name)` returns `true` when a global variable exists, otherwise it
returns `false`.

```javascript
if (!$.globals.has("base_url")) {
  console.warn("Global variable base_url is not configured");
}
```

### `$.globals.set(name, value)`

`$.globals.set(name, value)` sets a global variable. Values are stored as
strings by the scripting backend, so stringify objects before storing them.

```javascript
$.globals.set("base_url", "https://httpbin.org");
```

Store structured data as JSON:

```javascript
$.globals.set(
  "last_response_summary",
  JSON.stringify({
    code: $.response.code,
    time: $.response.responseTime,
  }),
);
```

### `$.globals.unset(name)`

`$.globals.unset(name)` removes a global variable. If the variable does not
exist, the call has no effect.

```javascript
$.globals.unset("temporary_token");
```

### `$.globals.clear()`

`$.globals.clear()` removes all global variables available through the global
variables backend. Use it carefully because global variables are workspace-level
values and may be shared by many requests.

```javascript
$.globals.clear();
```

### `$.globals.toObject()`

`$.globals.toObject()` returns all global variables as an object. Variable names
become object keys and variable values become string values.

```javascript
const globals = $.globals.toObject();
console.log("Global variable names:", Object.keys(globals));
```

## Postman Compatibility

### `pm.globals`

`pm.globals` exposes the same methods as `$.globals`: `get`, `has`, `set`,
`unset`, `clear`, and `toObject`.

```javascript
pm.globals.set("base_url", "https://httpbin.org");
const baseUrl = pm.globals.get("base_url");
console.log("Base URL:", baseUrl);
```

### Legacy `postman` Global Variable APIs

The legacy `postman` object exposes global variable helpers for older Postman
scripts:

- `postman.setGlobalVariable(name, value)`
- `postman.getGlobalVariable(name)`
- `postman.clearGlobalVariable(name)`

```javascript
postman.setGlobalVariable("base_url", "https://httpbin.org");
const baseUrl = postman.getGlobalVariable("base_url");
postman.clearGlobalVariable("base_url");
```

### Legacy `globals` Object

The global `globals` object exposes global variable values as object properties.
Use it for compatibility with older scripts that read globals directly.

```javascript
console.log("Base URL:", globals.base_url);
```

Prefer `$.globals.get(name)` in new scripts because it works through the
documented global variables API.

## Examples

### Use a Global Base URL

```javascript
const baseUrl = $.globals.get("base_url") || "https://httpbin.org";

$.request.headers.upsert({
  key: "X-Base-URL",
  value: baseUrl,
});
```

### Cache a Value for Later Requests

```javascript
const response = $.response.json();

if (response.id) {
  $.globals.set("last_created_id", response.id);
}
```

### Read and Parse Structured Data

```javascript
const rawConfig = $.globals.get("client_config");
const config = rawConfig ? JSON.parse(rawConfig) : {};

console.log("Client:", config.name || "not configured");
```

### Remove Temporary Globals

```javascript
$.globals.unset("temporary_token");
$.globals.unset("last_error");
```

## Guidance

- Prefer `$.globals` for new scripts.
- Use `pm.globals` and legacy `postman.*GlobalVariable()` APIs only for Postman
  migration compatibility.
- Global variable values are strings; use `JSON.stringify()` and `JSON.parse()`
  for structured values.
- Avoid `$.globals.clear()` unless the script intentionally owns all globals it
  might remove.
- Use environment, collection, folder, or iteration variables when a value
  should have a narrower scope than the whole workspace.

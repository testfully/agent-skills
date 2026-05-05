# Environment Variables

Use `$.environment` to work with variables from the active environment selected
for the request or Collection Runner run. Environment variables are useful for
values that change across local, staging, production, or per-user contexts, such
as hostnames, credentials, and tokens.

Testfully also exposes Postman-compatible environment variable APIs for
migration, but new scripts should prefer `$.environment`.

## APIs

| API                                           | Description                         |
| --------------------------------------------- | ----------------------------------- |
| `$.environment.get(name)`                     | Get an environment variable value.  |
| `$.environment.has(name)`                     | Check whether a variable exists.    |
| `$.environment.set(name, value)`              | Set an environment variable value.  |
| `$.environment.unset(name)`                   | Remove an environment variable.     |
| `$.environment.clear()`                       | Remove all environment variables.   |
| `$.environment.toObject()`                    | Return variables as a plain object. |
| `pm.environment.get(name)`                    | Postman-compatible alias.           |
| `pm.environment.has(name)`                    | Postman-compatible alias.           |
| `pm.environment.set(name, value)`             | Postman-compatible alias.           |
| `pm.environment.unset(name)`                  | Postman-compatible alias.           |
| `pm.environment.clear()`                      | Postman-compatible alias.           |
| `pm.environment.toObject()`                   | Postman-compatible alias.           |
| `postman.setEnvironmentVariable(name, value)` | Legacy Postman-compatible API.      |
| `postman.getEnvironmentVariable(name)`        | Legacy Postman-compatible API.      |
| `postman.clearEnvironmentVariable(name)`      | Legacy Postman-compatible API.      |

## Core APIs

### `$.environment.get(name)`

`$.environment.get(name)` returns the value of an environment variable as a
string. If the variable does not exist in the active environment, it returns
`undefined`.

```javascript
const baseUrl = $.environment.get("base_url");

if (baseUrl) {
  console.log("Environment base URL:", baseUrl);
}
```

### `$.environment.has(name)`

`$.environment.has(name)` returns `true` when the active environment contains a
variable with the provided name, otherwise it returns `false`.

```javascript
if (!$.environment.has("access_token")) {
  console.warn("No access token is available in the active environment");
}
```

### `$.environment.set(name, value)`

`$.environment.set(name, value)` sets an environment variable in the active
environment. Values are stored as strings by the scripting backend, so stringify
objects before storing them.

```javascript
$.environment.set("base_url", "https://httpbin.org");
```

Store structured data as JSON:

```javascript
$.environment.set(
  "last_login",
  JSON.stringify({
    code: $.response.code,
    time: new Date().toISOString(),
  }),
);
```

### `$.environment.unset(name)`

`$.environment.unset(name)` removes an environment variable from the active
environment. If the variable does not exist, the call has no effect.

```javascript
$.environment.unset("temporary_token");
```

### `$.environment.clear()`

`$.environment.clear()` removes all variables available through the active
environment backend. Use it carefully because it can remove values that other
requests depend on.

```javascript
$.environment.clear();
```

### `$.environment.toObject()`

`$.environment.toObject()` returns active environment variables as an object.
Variable names become object keys and variable values become string values.

```javascript
const environment = $.environment.toObject();
console.log("Environment variable names:", Object.keys(environment));
```

## Postman Compatibility

### `pm.environment`

`pm.environment` exposes the same methods as `$.environment`: `get`, `has`,
`set`, `unset`, `clear`, and `toObject`.

```javascript
pm.environment.set("base_url", "https://httpbin.org");
const baseUrl = pm.environment.get("base_url");
console.log("Base URL:", baseUrl);
```

### Legacy `postman` Environment Variable APIs

The legacy `postman` object exposes environment variable helpers for older
Postman scripts:

- `postman.setEnvironmentVariable(name, value)`
- `postman.getEnvironmentVariable(name)`
- `postman.clearEnvironmentVariable(name)`

```javascript
postman.setEnvironmentVariable("base_url", "https://httpbin.org");
const baseUrl = postman.getEnvironmentVariable("base_url");
postman.clearEnvironmentVariable("base_url");
```

## Examples

### Use an Environment Base URL

```javascript
const baseUrl = $.environment.get("base_url") || "https://httpbin.org";
console.log("Using:", baseUrl);
```

### Store a Token from a Response

```javascript
const data = $.response.json();

if (data.access_token) {
  $.environment.set("access_token", data.access_token);
}
```

### Add an Authorization Header

```javascript
const token = $.environment.get("access_token");

if (token) {
  $.request.headers.upsert({
    key: "Authorization",
    value: "Bearer " + token,
  });
}
```

### Read and Parse Structured Data

```javascript
const rawConfig = $.environment.get("client_config");
const config = rawConfig ? JSON.parse(rawConfig) : {};

console.log("Client:", config.name || "not configured");
```

### Remove Temporary Values

```javascript
$.environment.unset("temporary_token");
$.environment.unset("last_login");
```

## Guidance

- Prefer `$.environment` for new scripts.
- Use `pm.environment` and legacy `postman.*EnvironmentVariable()` APIs only for
  Postman migration compatibility.
- Environment variable values are strings; use `JSON.stringify()` and
  `JSON.parse()` for structured values.
- Avoid `$.environment.clear()` unless the script intentionally owns all active
  environment variables it might remove.
- Use global variables for workspace-wide values and environment variables for
  values that should change with the selected environment.

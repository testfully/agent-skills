# Folder And Collection Variables

Use `$.parentFolder.variables` and `$.rootFolder.variables` to work with
variables defined closer to the request than workspace-level globals or active
environment variables.

In Testfully, root folder variables are collection variables. The scripting API
still exposes them as `$.rootFolder.variables`; Postman-compatible scripts can
use `pm.collectionVariables`.

## APIs

| API                                         | Description                         |
| ------------------------------------------- | ----------------------------------- |
| `$.parentFolder.variables.get(name)`        | Get a parent folder variable.       |
| `$.parentFolder.variables.has(name)`        | Check whether a variable exists.    |
| `$.parentFolder.variables.set(name, value)` | Set a parent folder variable.       |
| `$.parentFolder.variables.unset(name)`      | Remove a parent folder variable.    |
| `$.parentFolder.variables.clear()`          | Remove parent folder variables.     |
| `$.parentFolder.variables.toObject()`       | Return variables as a plain object. |
| `$.rootFolder.variables.get(name)`          | Get a collection variable.          |
| `$.rootFolder.variables.has(name)`          | Check whether a variable exists.    |
| `$.rootFolder.variables.set(name, value)`   | Set a collection variable.          |
| `$.rootFolder.variables.unset(name)`        | Remove a collection variable.       |
| `$.rootFolder.variables.clear()`            | Remove collection variables.        |
| `$.rootFolder.variables.toObject()`         | Return variables as a plain object. |
| `pm.collectionVariables.get(name)`          | Postman-compatible alias.           |
| `pm.collectionVariables.has(name)`          | Postman-compatible alias.           |
| `pm.collectionVariables.set(name, value)`   | Postman-compatible alias.           |
| `pm.collectionVariables.unset(name)`        | Postman-compatible alias.           |
| `pm.collectionVariables.clear()`            | Postman-compatible alias.           |
| `pm.collectionVariables.toObject()`         | Postman-compatible alias.           |

All APIs above can also work with user-scoped variables by passing `true` as the
last argument.

## Parent Folder Variables

### `$.parentFolder.variables.get(name)`

Returns the value of a variable defined on the request's parent folder as a
string. If the variable does not exist, it returns `undefined`.

```javascript
const folderToken = $.parentFolder.variables.get("access_token");
console.log("Folder token:", folderToken);
```

### `$.parentFolder.variables.has(name)`

Returns `true` when the parent folder contains a variable with the provided
name, otherwise it returns `false`.

```javascript
if (!$.parentFolder.variables.has("access_token")) {
  console.warn("Parent folder access_token is not configured");
}
```

### `$.parentFolder.variables.set(name, value)`

Sets a variable on the parent folder. Values are stored as strings by the
scripting backend, so stringify objects before storing them.

```javascript
$.parentFolder.variables.set("access_token", "token-value");
```

### `$.parentFolder.variables.unset(name)`

Removes a variable from the parent folder. If the variable does not exist, the
call has no effect.

```javascript
$.parentFolder.variables.unset("temporary_token");
```

### `$.parentFolder.variables.clear()`

Removes all variables available through the parent folder variables backend. Use
it carefully because it can remove values used by other requests in the folder.

```javascript
$.parentFolder.variables.clear();
```

### `$.parentFolder.variables.toObject()`

Returns parent folder variables as an object. Variable names become object keys
and variable values become string values.

```javascript
const variables = $.parentFolder.variables.toObject();
console.log("Parent folder variable names:", Object.keys(variables));
```

## Collection Variables

### `$.rootFolder.variables.get(name)`

Returns the value of a collection variable as a string. If the variable does not
exist, it returns `undefined`.

```javascript
const collectionToken = $.rootFolder.variables.get("access_token");
console.log("Collection token:", collectionToken);
```

### `$.rootFolder.variables.has(name)`

Returns `true` when the collection contains a variable with the provided name,
otherwise it returns `false`.

```javascript
if ($.rootFolder.variables.has("base_url")) {
  console.log("Collection base URL is configured");
}
```

### `$.rootFolder.variables.set(name, value)`

Sets a collection variable. Values are stored as strings by the scripting
backend, so stringify objects before storing them.

```javascript
$.rootFolder.variables.set("base_url", "https://httpbin.org");
```

### `$.rootFolder.variables.unset(name)`

Removes a collection variable. If the variable does not exist, the call has no
effect.

```javascript
$.rootFolder.variables.unset("temporary_token");
```

### `$.rootFolder.variables.clear()`

Removes all collection variables available through the collection variables
backend. Use it carefully because collection variables may be shared by many
requests.

```javascript
$.rootFolder.variables.clear();
```

### `$.rootFolder.variables.toObject()`

Returns collection variables as an object. Variable names become object keys and
variable values become string values.

```javascript
const variables = $.rootFolder.variables.toObject();
console.log("Collection variable names:", Object.keys(variables));
```

## Postman Compatibility

### `pm.collectionVariables`

`pm.collectionVariables` is a Postman-compatible alias for collection variables.
It exposes `get`, `has`, `set`, `unset`, `clear`, and `toObject`.

```javascript
pm.collectionVariables.set("base_url", "https://httpbin.org");
const baseUrl = pm.collectionVariables.get("base_url");
console.log("Collection base URL:", baseUrl);
```

Prefer `$.rootFolder.variables` in new Testfully scripts.

## User-Scoped Variables

Folder and collection variables can be user-scoped. Pass `true` as the last
argument to read or write the user-scoped value for the current user.

```javascript
$.parentFolder.variables.set("access_token", "folder-user-token", true);
const folderToken = $.parentFolder.variables.get("access_token", true);

$.rootFolder.variables.set("access_token", "collection-user-token", true);
const collectionToken = $.rootFolder.variables.get("access_token", true);

console.log(folderToken, collectionToken);
```

The optional user-scoped flag is supported by:

- `get(name, true)`
- `has(name, true)`
- `set(name, value, true)`
- `unset(name, true)`
- `clear(true)`
- `toObject(true)`

## Examples

### Use a Collection Base URL

```javascript
const baseUrl = $.rootFolder.variables.get("base_url") || "https://httpbin.org";

$.request.headers.upsert({
  key: "X-Base-URL",
  value: baseUrl,
});
```

### Store a Parent Folder Token

```javascript
const data = $.response.json();

if (data.access_token) {
  $.parentFolder.variables.set("access_token", data.access_token);
}
```

### Read Variables by Scope

```javascript
const folderVariables = $.parentFolder.variables.toObject();
const collectionVariables = $.rootFolder.variables.toObject();

console.log("Folder variables:", Object.keys(folderVariables));
console.log("Collection variables:", Object.keys(collectionVariables));
```

### Use Postman-Compatible Collection Variables

```javascript
pm.collectionVariables.set("base_url", "https://httpbin.org");
const baseUrl = pm.collectionVariables.get("base_url");
console.log("Base URL:", baseUrl);
```

## Guidance

- Use `$.parentFolder.variables` for values shared by requests in the same
  folder.
- Use `$.rootFolder.variables` for collection variables shared across the
  collection.
- Use `pm.collectionVariables` only for Postman migration compatibility.
- Values are strings; use `JSON.stringify()` and `JSON.parse()` for structured
  data.
- Avoid `clear()` unless the script intentionally owns every variable it might
  remove.
- Use user-scoped variables for per-user secrets or tokens when the workspace
  plan supports them.

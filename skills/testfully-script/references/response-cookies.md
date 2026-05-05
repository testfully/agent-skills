# Response Cookies

Use the `$.cookies` APIs to retrieve cookies returned by the current response.
These APIs are intended for After Response scripts, where Testfully has received
the response and can expose its cookies.

For raw cookie inspection, `$.response.cookies` is also available as an array of
cookie objects with `name` and `value` fields. Use `$.cookies` when you want a
convenient lookup API.

## APIs

| API                     | Description                       |
| ----------------------- | --------------------------------- |
| `$.cookies.get(name)`   | Get a response cookie value.      |
| `$.cookies.has(name)`   | Check whether a cookie exists.    |
| `$.cookies.toObject()`  | Return cookies as a plain object. |
| `pm.cookies.get(name)`  | Postman-compatible alias.         |
| `pm.cookies.has(name)`  | Postman-compatible alias.         |
| `pm.cookies.toObject()` | Postman-compatible alias.         |

Prefer `$.cookies` for new Testfully scripts. Use `pm.cookies` when migrating
Postman scripts.

## Cookie Access

### `$.cookies.get(name)`

`$.cookies.get(name)` returns the value of the response cookie with the provided
name. Cookie names are case-sensitive.

If no cookie exists for the provided name, Testfully returns an empty string.

```javascript
const csrfToken = $.cookies.get("x-csrf-token");

if (csrfToken) {
  console.log("CSRF token:", csrfToken);
}
```

### `$.cookies.has(name)`

`$.cookies.has(name)` returns `true` when the response contains a cookie with
the provided name, otherwise it returns `false`.

```javascript
if ($.cookies.has("session")) {
  console.log("Session cookie was returned");
}
```

### `$.cookies.toObject()`

`$.cookies.toObject()` returns all response cookies as an object. Cookie names
become object keys and cookie values become object values.

```javascript
const cookies = $.cookies.toObject();
console.log("Cookie names:", Object.keys(cookies));
```

### `pm.cookies`

`pm.cookies.get(name)`, `pm.cookies.has(name)`, and `pm.cookies.toObject()` are
Postman-compatible aliases for the same response cookie lookup behavior.

```javascript
const session = pm.cookies.get("session");
console.log("Session:", session);
```

### `$.response.cookies`

`$.response.cookies` is an array of response cookie objects. Use it when you
need to iterate through cookies or inspect both names and values together.

```javascript
const cookie = $.response.cookies.find((item) => item.name === "session");
console.log("Session cookie:", cookie ? cookie.value : "not set");
```

## Examples

### Read a Cookie from an `httpbin.org` Response

Point a request at `https://httpbin.org/cookies/set/session/example`, then use
this in an After Response script:

```javascript
const session = $.cookies.get("session");
console.log("Session cookie:", session);
```

### Assert That a Cookie Exists

```javascript
$.test("Session cookie exists", function () {
  $.expect($.cookies.has("session")).to.equal(true);
});
```

### Store a Cookie in an Environment Variable

```javascript
const token = $.cookies.get("x-csrf-token");

if (token) {
  $.environment.set("csrf_token", token);
}
```

### Convert Cookies to an Object

```javascript
const cookies = $.cookies.toObject();

if (cookies.session) {
  console.log("Session cookie value:", cookies.session);
}
```

## Guidance

- Use these APIs in After Response scripts.
- Cookie names are case-sensitive.
- Use `$.cookies.has(name)` before `$.cookies.get(name)` when an empty cookie
  value would be ambiguous.
- Use `$.response.cookies` when you need array-style iteration.
- Use `pm.cookies` aliases only for Postman migration compatibility.

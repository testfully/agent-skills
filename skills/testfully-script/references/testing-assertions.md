# Testing And Assertions

Use `$.test()` and `$.expect()` to write validations in Testfully scripts. These
APIs are most useful in After Response scripts, where the current response is
available through `$.response`.

Testfully uses the Chai assertion style for `expect` assertions. Prefer the `$`
APIs for new Testfully scripts, and use the Postman-compatible APIs when
migrating existing Postman scripts.

## APIs

| API                               | Description                                |
| --------------------------------- | ------------------------------------------ |
| `$.test(name, callback)`          | Define a named validation.                 |
| `$.expect(value)`                 | Start a Chai-style assertion chain.        |
| `pm.test(name, callback)`         | Postman-compatible alias for `$.test()`.   |
| `pm.expect(value)`                | Postman-compatible alias for `$.expect()`. |
| `tests[name] = assertion`         | Legacy Postman-compatible test object.     |
| `responseBody`                    | Legacy Postman response body global.       |
| `responseHeaders`                 | Legacy Postman response headers global.    |
| `responseCode`                    | Legacy Postman response code global.       |
| `responseTime`                    | Legacy Postman response time global.       |
| `postman.getResponseHeader(name)` | Legacy Postman response header helper.     |

## Core APIs

### `$.test(name, callback)`

`$.test()` creates a named validation. Testfully records the validation as
passed when the callback finishes without throwing. If an assertion throws,
Testfully records the validation as failed and includes the error message.

```javascript
$.test("Status code is 200", function () {
  $.expect($.response.code).to.equal(200);
});
```

The first argument should be a non-empty description. The second argument must
be a function. When either value is missing or invalid, Testfully logs a warning
instead of recording a validation.

### `$.expect(value)`

`$.expect()` starts a Chai-style assertion chain for the supplied value. Use it
inside `$.test()` so assertion failures are reported as validation failures.

```javascript
$.test("Response contains request metadata", function () {
  const data = $.response.json();

  $.expect(data).to.be.an("object");
  $.expect(data.url).to.include("https://httpbin.org/anything");
  $.expect(data.method).to.equal("GET");
});
```

Common assertion patterns include:

```javascript
$.expect($.response.code).to.equal(200);
$.expect($.response.responseTime).to.be.below(1000);
$.expect($.response.headers).to.be.an("array");
$.expect($.response.text()).to.include("httpbin");
$.expect($.response.json()).to.have.property("url");
```

For the complete assertion chain syntax, use Chai's BDD `expect` API.

## Postman-Compatible APIs

### `pm.test(name, callback)`

`pm.test()` is a Postman-compatible alias for `$.test()`. It records validations
the same way.

```javascript
pm.test("Status code is 200", function () {
  pm.expect(pm.response.code).to.equal(200);
});
```

### `pm.expect(value)`

`pm.expect()` is a Postman-compatible alias for `$.expect()`. It exposes the
same Chai-style assertion methods.

```javascript
pm.test("Response is JSON", function () {
  const data = pm.response.json();

  pm.expect(data).to.be.an("object");
  pm.expect(data.url).to.include("https://httpbin.org/anything");
});
```

### `tests[name] = assertion`

The legacy Postman `tests` object is available for migrated scripts. Assign a
boolean expression or a function to a test name.

```javascript
tests["Status code is 200"] = responseCode.code === 200;

tests["Body includes echoed URL"] = function () {
  pm.expect(responseBody).to.include("https://httpbin.org/anything");
};
```

When the assigned value is a function, Testfully runs it as the validation body.
When the assigned value is not a function, Testfully expects it to be `true`.

Prefer `$.test()` for new scripts because it is clearer and easier to refactor.

## Legacy Postman Response Globals

Older Postman scripts often combine the `tests` object with response globals.
Testfully exposes these globals for compatibility.

### `responseBody`

`responseBody` is the response body string. It also supports
`responseBody.has(pattern)`, which returns `true` when the body contains the
given text.

```javascript
tests["Body contains echoed URL"] = responseBody.has(
  "https://httpbin.org/anything",
);
```

### `responseHeaders`

`responseHeaders` is an object keyed by the response header names as Testfully
received them.

```javascript
tests["Response has Content-Type"] = Boolean(
  responseHeaders["content-type"] || responseHeaders["Content-Type"],
);
```

### `responseCode`

`responseCode` is an object with HTTP status metadata:

| Field                       | Description                 |
| --------------------------- | --------------------------- |
| `responseCode.code`         | Numeric HTTP status code.   |
| `responseCode.name`         | HTTP status name.           |
| `responseCode.standardName` | Standard HTTP status name.  |
| `responseCode.detail`       | HTTP status detail message. |

```javascript
tests["Status code is 200"] = responseCode.code === 200;
```

### `responseTime`

`responseTime` is the response duration in milliseconds.

```javascript
tests["Response is fast"] = responseTime < 1000;
```

### `postman.getResponseHeader(name)`

`postman.getResponseHeader(name)` returns a response header value by name.
Header name matching is case-insensitive. Missing headers return an empty
string.

```javascript
tests["Content-Type exists"] = postman.getResponseHeader("content-type") !== "";
```

## Examples

### Validate a Request to `httpbin.org`

Point a request at `https://httpbin.org/anything`, then add this After Response
script:

```javascript
$.test("httpbin returns the echoed request", function () {
  $.expect($.response.code).to.equal(200);
  $.expect($.response.responseTime).to.be.below(5000);

  const data = $.response.json();
  $.expect(data.url).to.equal("https://httpbin.org/anything");
});
```

### Validate a JSON POST Body

Send a `POST` request to `https://httpbin.org/anything` with a JSON body, then
use this After Response script:

```javascript
$.test("httpbin echoes JSON payload", function () {
  const data = $.response.json();

  $.expect(data.method).to.equal("POST");
  $.expect(data.json).to.be.an("object");
  $.expect(data.json.status).to.equal("active");
});
```

### Migrated Postman Test

```javascript
pm.test("Status code is 200", function () {
  pm.expect(pm.response.code).to.equal(200);
  pm.expect(pm.response.json()).to.have.property("url");
});
```

## Guidance

- Prefer `$.test()` and `$.expect()` for new Testfully scripts.
- Keep assertions inside `$.test()` so Testfully records pass and fail results.
- Use `pm.test()` and `pm.expect()` when migrating Postman scripts.
- Keep response validations in After Response scripts.
- Use legacy globals such as `tests`, `responseBody`, and `responseCode` only
  when preserving older Postman scripts.

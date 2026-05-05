---
name: testfully-script
description:
  A skill for writing JavaScript code that uses Testfully's built-in scripting
  capabilities to automate tasks and processes.
license: MIT
metadata:
  author: testfully
  version: "1.0.0"
---

# Testfully Script Skill

A comprehensive skill for writing JavaScript code that uses Testfully's built-in
scripting capabilities to automate tasks and processes as part of Before Request
and After Response script hooks in Testfully.

## When to Apply

Use and reference this skill and guidelines when:

- Writing or refactoring JavaScript code to be used in Testfully's Before
  Request and After Response script hooks.
- Extending Testfully's features and capabilities beyond what is available
  through the UI by leveraging custom scripts.

## API Documentation Workflow

When introducing a new Testfully scripting API or a related set of APIs:

1. Add it to the Quick Reference section first.
2. Add a dedicated markdown file under `references/` for the full API
   documentation.
3. Link each Quick Reference item to the new reference file.

## Quick Reference

### Console Logging

Use these APIs to record diagnostic information from Before Request and After
Response scripts in the request Logs tab.

- `console.log(...args)` - Writes informational logs to the request Logs tab
  (Desktop and Web) or terminal (CLI). See
  [`references/console.md`](references/console.md).
- `console.info(...args)` - Writes informational logs to the request Logs tab
  (Desktop and Web) or terminal (CLI). See
  [`references/console.md`](references/console.md).
- `console.warn(...args)` - Writes warning logs to the request Logs tab (Desktop
  and Web) or terminal (CLI). See
  [`references/console.md`](references/console.md).
- `console.error(...args)` - Writes error logs to the request Logs tab (Desktop
  and Web) or terminal (CLI). See
  [`references/console.md`](references/console.md).

### HTTP Requests

Use these APIs to send bespoke HTTP requests from scripts without changing the
current request.

- `$.sendRequest(request)` - Sends a URL or request object and returns the HTTP
  response. See [`references/send-request.md`](references/send-request.md).
- `pm.sendRequest(request)` - Postman-compatible alias for `$.sendRequest()`.
  See [`references/send-request.md`](references/send-request.md).
- `sendRequest(request)` - Legacy global alias for `$.sendRequest()`. See
  [`references/send-request.md`](references/send-request.md).

### Current Request

Use these APIs to inspect the outgoing request and update request headers from
scripts.

- `$.request` - Access the current request snapshot, including method, URL,
  headers, query params, type, and body.
- `pm.request` - Postman-compatible alias for `$.request`.
- `$.request.method` - Read the outgoing request HTTP method.
- `$.request.url` - Read the outgoing request URL.
- `$.request.type` - Read the outgoing request body type.
- `$.request.headers` - Read the outgoing request headers.
- `$.request.headers.add({ key, value })` - Append a request header.
- `$.request.headers.upsert({ key, value })` - Add or update a request header.
- `$.request.headers.remove(key)` - Remove a request header by name.
- `$.request.params` - Read outgoing request query parameters.
- `$.request.body` - Read the outgoing request body fields.
- `$.request.skip()` - Skip the current request from a Before Request script.
- `pm.execution.skipRequest()` - Postman-compatible alias for
  `$.request.skip()`.

See [`references/request.md`](references/request.md).

### Current Response

Use these APIs to inspect the response returned by the current request from
After Response scripts.

- `$.response` - Access the current response snapshot.
- `pm.response` - Postman-compatible alias for `$.response`.
- `$.response.code` - Read the HTTP status code.
- `$.response.status` - Read the HTTP status text.
- `$.response.headers` - Read response headers.
- `$.response.body` - Read the response body as a string.
- `$.response.cookies` - Read response cookies.
- `$.response.text()` - Return the response body as a string.
- `$.response.json()` - Parse and return the response body as JSON.
- `$.response.responseTime` - Read response duration in milliseconds.
- `$.response.responseSize` - Read response size.

See [`references/response.md`](references/response.md).

### Testing And Assertions

Use these APIs to record validations and write Chai-style assertions in After
Response scripts.

- `$.test(name, callback)` - Define a named validation.
- `$.expect(value)` - Start a Chai-style assertion chain.
- `pm.test(name, callback)` - Postman-compatible alias for `$.test()`.
- `pm.expect(value)` - Postman-compatible alias for `$.expect()`.
- `tests[name] = assertion` - Legacy Postman-compatible test object.
- `responseBody` - Legacy Postman-compatible response body global.
- `responseHeaders` - Legacy Postman-compatible response headers global.
- `responseCode` - Legacy Postman-compatible response code global.
- `responseTime` - Legacy Postman-compatible response time global.
- `postman.getResponseHeader(name)` - Legacy Postman-compatible response header
  helper.

See [`references/testing-assertions.md`](references/testing-assertions.md).

### Postman Script Info

Use this Postman-compatible object to inspect basic script execution metadata.

- `pm.info` - Access script execution metadata.
- `pm.info.eventName` - Read the current script phase.
- `pm.info.requestName` - Read the current request name.
- `pm.info.requestId` - Read the current request id.
- `pm.info.iteration` - Read the current iteration number.
- `pm.info.iterationCount` - Read the total iteration count.

See [`references/pm-info.md`](references/pm-info.md).

### Multi-Step Requests

Use this API to read request and response data from completed steps in a
multi-step request.

- `$.steps(stepIndex)` - Return request and response information for a completed
  step by zero-based index.

See [`references/steps.md`](references/steps.md).

### Response Cookies

Use these APIs to retrieve cookies returned by the current response from After
Response scripts.

- `$.cookies.get(name)` - Get a response cookie value by name.
- `$.cookies.has(name)` - Check whether a response cookie exists.
- `$.cookies.toObject()` - Return response cookies as an object keyed by cookie
  name.
- `pm.cookies.get(name)` - Postman-compatible alias for `$.cookies.get()`.
- `pm.cookies.has(name)` - Postman-compatible alias for `$.cookies.has()`.
- `pm.cookies.toObject()` - Postman-compatible alias for `$.cookies.toObject()`.

See [`references/response-cookies.md`](references/response-cookies.md).

### Global Variables

Use these APIs to read and update workspace-level global variables from scripts.

- `$.globals.get(name)` - Get a global variable value.
- `$.globals.has(name)` - Check whether a global variable exists.
- `$.globals.set(name, value)` - Set a global variable value.
- `$.globals.unset(name)` - Remove a global variable.
- `$.globals.clear()` - Remove all global variables.
- `$.globals.toObject()` - Return global variables as an object.
- `pm.globals.*` - Postman-compatible aliases for `$.globals.*`.
- `postman.*GlobalVariable(...)` - Legacy Postman-compatible global variable
  helpers.
- `globals` - Legacy global object containing global variable values.

See [`references/globals.md`](references/globals.md).

### Globally Scoped Script Values

Use `globalThis` to expose JavaScript constants and helper functions to scripts
within the same collection or folder tree.

- `globalThis.NAME = value` - Define a globally scoped script value.
- `globalThis.helper = function (...) { ... }` - Define a globally scoped helper
  function.
- `NAME` - Read a globally scoped script value after it has been defined.

See [`references/global-this.md`](references/global-this.md).

### Environment Variables

Use these APIs to read and update variables from the active environment.

- `$.environment.get(name)` - Get an environment variable value.
- `$.environment.has(name)` - Check whether an environment variable exists.
- `$.environment.set(name, value)` - Set an environment variable value.
- `$.environment.unset(name)` - Remove an environment variable.
- `$.environment.clear()` - Remove all environment variables.
- `$.environment.toObject()` - Return environment variables as an object.
- `pm.environment.*` - Postman-compatible aliases for `$.environment.*`.
- `postman.*EnvironmentVariable(...)` - Legacy Postman-compatible environment
  variable helpers.

See [`references/environment.md`](references/environment.md).

### Folder And Collection Variables

Use these APIs to read and update variables from the parent folder or
collection.

- `$.parentFolder.variables.*` - Work with variables on the request's parent
  folder.
- `$.rootFolder.variables.*` - Work with collection variables.
- `pm.collectionVariables.*` - Postman-compatible aliases for
  `$.rootFolder.variables.*`.

See
[`references/folder-collection-variables.md`](references/folder-collection-variables.md).

### npm Packages

Use standard ES module imports to load public npm packages that work in a
browser-style JavaScript sandbox.

- `import value from "package-name"` - Import a package default export.
- `import { value } from "package-name"` - Import named exports.
- `import value from "package-name@version"` - Import a specific package
  version.

See [`references/npm-packages.md`](references/npm-packages.md).

### Miscellaneous Timing APIs

Use these APIs to delay script execution or schedule callbacks inside the
Testfully sandbox.

- `$.sleep(ms)` - Pause script execution for a positive number of milliseconds.
- `setTimeout(callback, ms)` - Run a callback once after a delay.
- `clearTimeout(timeoutId)` - Cancel a pending timeout.
- `setInterval(callback, ms)` - Run a callback repeatedly at an interval.
- `clearInterval(intervalId)` - Cancel a running interval.

See [`references/sleep.md`](references/sleep.md),
[`references/set-timeout.md`](references/set-timeout.md), and
[`references/set-interval.md`](references/set-interval.md).

### Collection Runner Flow Control

Use these APIs to choose which request runs next while a collection is running
serially in Collection Runner.

- `$.setNextRequest(requestIdOrTitle)` - Set the next request by id or title.
- `pm.setNextRequest(requestIdOrTitle)` - Postman-compatible alias for
  `$.setNextRequest()`.
- `postman.setNextRequest(requestIdOrTitle)` - Legacy Postman-compatible alias
  for `$.setNextRequest()`.

See [`references/set-next-request.md`](references/set-next-request.md).

## How to Use

Use the "Quick Reference" section above to understand the APIs and capabilities
of Testfully's scripting environment. Read individual API documentation for
detailed explanations, examples, and best practices for using each API
effectively in your scripts.

```
references/console.md
references/environment.md
references/folder-collection-variables.md
references/global-this.md
references/globals.md
references/npm-packages.md
references/pm-info.md
references/request.md
references/response.md
references/response-cookies.md
references/set-next-request.md
references/set-interval.md
references/set-timeout.md
references/send-request.md
references/sleep.md
references/steps.md
references/testing-assertions.md
```

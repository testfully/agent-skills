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

## Quick Reference

Below is a quick reference to the key APIs and capabilities of Testfully's
scripting environment. Use this as the starting point and deep dive into
individual API documentation using the "Learn more" links for detailed
explanations and examples.

### Console Logging

Use these APIs to record diagnostic information from Before Request and After
Response scripts in the request Logs tab.

| API                      | Description                                               |
| ------------------------ | --------------------------------------------------------- |
| `console.log(...args)`   | Write informational logs to the Logs tab or CLI terminal. |
| `console.info(...args)`  | Write informational logs to the Logs tab or CLI terminal. |
| `console.warn(...args)`  | Write warning logs to the Logs tab or CLI terminal.       |
| `console.error(...args)` | Write error logs to the Logs tab or CLI terminal.         |

Learn more: [`references/console.md`](references/console.md).

### HTTP Requests

Use these APIs to send bespoke HTTP requests from scripts without changing the
current request.

| API                       | Description                                         |
| ------------------------- | --------------------------------------------------- |
| `$.sendRequest(request)`  | Send a URL or request object and return a response. |
| `pm.sendRequest(request)` | Postman-compatible alias for `$.sendRequest()`.     |
| `sendRequest(request)`    | Legacy global alias for `$.sendRequest()`.          |

Learn more: [`references/send-request.md`](references/send-request.md).

### Current Request

Use these APIs to inspect the outgoing request and update request headers from
scripts.

| API                                        | Description                                      |
| ------------------------------------------ | ------------------------------------------------ |
| `$.request`                                | Access the current request snapshot.             |
| `pm.request`                               | Postman-compatible alias for `$.request`.        |
| `$.request.method`                         | Read the outgoing request HTTP method.           |
| `$.request.url`                            | Read the outgoing request URL.                   |
| `$.request.type`                           | Read the outgoing request body type.             |
| `$.request.headers`                        | Read the outgoing request headers.               |
| `$.request.headers.add({ key, value })`    | Append a request header.                         |
| `$.request.headers.upsert({ key, value })` | Add or update a request header.                  |
| `$.request.headers.remove(key)`            | Remove a request header by name.                 |
| `$.request.params`                         | Read outgoing request query parameters.          |
| `$.request.body`                           | Read the outgoing request body fields.           |
| `$.request.skip()`                         | Skip the current request from Before Request.    |
| `pm.execution.skipRequest()`               | Postman-compatible alias for `$.request.skip()`. |

Learn more: [`references/request.md`](references/request.md).

### Current Response

Use these APIs to inspect the response returned by the current request from
After Response scripts.

| API                       | Description                                 |
| ------------------------- | ------------------------------------------- |
| `$.response`              | Access the current response snapshot.       |
| `pm.response`             | Postman-compatible alias for `$.response`.  |
| `$.response.code`         | Read the HTTP status code.                  |
| `$.response.status`       | Read the HTTP status text.                  |
| `$.response.headers`      | Read response headers.                      |
| `$.response.body`         | Read the response body as a string.         |
| `$.response.cookies`      | Read response cookies.                      |
| `$.response.text()`       | Return the response body as a string.       |
| `$.response.json()`       | Parse and return the response body as JSON. |
| `$.response.responseTime` | Read response duration in milliseconds.     |
| `$.response.responseSize` | Read response size.                         |

Learn more: [`references/response.md`](references/response.md).

### Testing And Assertions

Use these APIs to record validations and write Chai-style assertions in After
Response scripts.

| API                               | Description                                        |
| --------------------------------- | -------------------------------------------------- |
| `$.test(name, callback)`          | Define a named validation.                         |
| `$.expect(value)`                 | Start a Chai-style assertion chain.                |
| `pm.test(name, callback)`         | Postman-compatible alias for `$.test()`.           |
| `pm.expect(value)`                | Postman-compatible alias for `$.expect()`.         |
| `tests[name] = assertion`         | Legacy Postman-compatible test object.             |
| `responseBody`                    | Legacy Postman-compatible response body global.    |
| `responseHeaders`                 | Legacy Postman-compatible response headers global. |
| `responseCode`                    | Legacy Postman-compatible response code global.    |
| `responseTime`                    | Legacy Postman-compatible response time global.    |
| `postman.getResponseHeader(name)` | Legacy Postman-compatible response header helper.  |

Learn more:
[`references/testing-assertions.md`](references/testing-assertions.md).

### Postman Script Info

Use this Postman-compatible object to inspect basic script execution metadata.

| API                      | Description                        |
| ------------------------ | ---------------------------------- |
| `pm.info`                | Access script execution metadata.  |
| `pm.info.eventName`      | Read the current script phase.     |
| `pm.info.requestName`    | Read the current request name.     |
| `pm.info.requestId`      | Read the current request id.       |
| `pm.info.iteration`      | Read the current iteration number. |
| `pm.info.iterationCount` | Read the total iteration count.    |

Learn more: [`references/pm-info.md`](references/pm-info.md).

### Iteration Data

Use these APIs to read and update the current Collection Runner iteration data
from scripts.

| API                               | Description                                         |
| --------------------------------- | --------------------------------------------------- |
| `$.iterationData.get(key)`        | Get a value from the current iteration data.        |
| `$.iterationData.has(key)`        | Check whether a key exists.                         |
| `$.iterationData.set(key, value)` | Set a value in the current iteration data.          |
| `$.iterationData.unset(key)`      | Remove a value from the current iteration data.     |
| `$.iterationData.toObject()`      | Return the current iteration data as an object.     |
| `$.iterationData.toJSON()`        | Return the current iteration data as JSON.          |
| `pm.iterationData.*`              | Postman-compatible aliases for `$.iterationData.*`. |

Learn more: [`references/iteration-data.md`](references/iteration-data.md).

### Multi-Step Requests

Use this API to read request and response data from completed steps in a
multi-step request.

| API                  | Description                                     |
| -------------------- | ----------------------------------------------- |
| `$.steps(stepIndex)` | Return completed step data by zero-based index. |

Learn more: [`references/steps.md`](references/steps.md).

### Response Cookies

Use these APIs to retrieve cookies returned by the current response from After
Response scripts.

| API                     | Description                                          |
| ----------------------- | ---------------------------------------------------- |
| `$.cookies.get(name)`   | Get a response cookie value by name.                 |
| `$.cookies.has(name)`   | Check whether a response cookie exists.              |
| `$.cookies.toObject()`  | Return response cookies as an object.                |
| `pm.cookies.get(name)`  | Postman-compatible alias for `$.cookies.get()`.      |
| `pm.cookies.has(name)`  | Postman-compatible alias for `$.cookies.has()`.      |
| `pm.cookies.toObject()` | Postman-compatible alias for `$.cookies.toObject()`. |

Learn more: [`references/response-cookies.md`](references/response-cookies.md).

### Global Variables

Use these APIs to read and update workspace-level global variables from scripts.

| API                            | Description                                        |
| ------------------------------ | -------------------------------------------------- |
| `$.globals.get(name)`          | Get a global variable value.                       |
| `$.globals.has(name)`          | Check whether a global variable exists.            |
| `$.globals.set(name, value)`   | Set a global variable value.                       |
| `$.globals.unset(name)`        | Remove a global variable.                          |
| `$.globals.clear()`            | Remove all global variables.                       |
| `$.globals.toObject()`         | Return global variables as an object.              |
| `pm.globals.*`                 | Postman-compatible aliases for `$.globals.*`.      |
| `postman.*GlobalVariable(...)` | Legacy Postman-compatible global variable helpers. |
| `globals`                      | Legacy global object containing global variables.  |

Learn more: [`references/globals.md`](references/globals.md).

### Globally Scoped Script Values

Use `globalThis` to expose JavaScript constants and helper functions to scripts
within the same collection or folder tree.

| API                                          | Description                               |
| -------------------------------------------- | ----------------------------------------- |
| `globalThis.NAME = value`                    | Define a globally scoped script value.    |
| `globalThis.helper = function (...) { ... }` | Define a globally scoped helper function. |
| `NAME`                                       | Read a globally scoped script value.      |

Learn more: [`references/global-this.md`](references/global-this.md).

### Environment Variables

Use these APIs to read and update variables from the active environment.

| API                                 | Description                                       |
| ----------------------------------- | ------------------------------------------------- |
| `$.environment.get(name)`           | Get an environment variable value.                |
| `$.environment.has(name)`           | Check whether an environment variable exists.     |
| `$.environment.set(name, value)`    | Set an environment variable value.                |
| `$.environment.unset(name)`         | Remove an environment variable.                   |
| `$.environment.clear()`             | Remove all environment variables.                 |
| `$.environment.toObject()`          | Return environment variables as an object.        |
| `pm.environment.*`                  | Postman-compatible aliases for `$.environment.*`. |
| `postman.*EnvironmentVariable(...)` | Legacy Postman-compatible environment helpers.    |

Learn more: [`references/environment.md`](references/environment.md).

### Folder And Collection Variables

Use these APIs to read and update variables from the parent folder or
collection.

| API                          | Description                                          |
| ---------------------------- | ---------------------------------------------------- |
| `$.parentFolder.variables.*` | Work with variables on the request's parent folder.  |
| `$.rootFolder.variables.*`   | Work with collection variables.                      |
| `pm.collectionVariables.*`   | Postman-compatible aliases for collection variables. |

Learn more:
[`references/folder-collection-variables.md`](references/folder-collection-variables.md).

### npm Packages

Use standard ES module imports to load public npm packages that work in a
browser-style JavaScript sandbox.

| API                                        | Description                        |
| ------------------------------------------ | ---------------------------------- |
| `import value from "package-name"`         | Import a package default export.   |
| `import { value } from "package-name"`     | Import named exports.              |
| `import value from "package-name@version"` | Import a specific package version. |

Learn more: [`references/npm-packages.md`](references/npm-packages.md).

### Miscellaneous Timing APIs

Use these APIs to delay script execution or schedule callbacks inside the
Testfully sandbox.

| API                         | Description                                       |
| --------------------------- | ------------------------------------------------- |
| `$.sleep(ms)`               | Pause script execution for positive milliseconds. |
| `setTimeout(callback, ms)`  | Run a callback once after a delay.                |
| `clearTimeout(timeoutId)`   | Cancel a pending timeout.                         |
| `setInterval(callback, ms)` | Run a callback repeatedly at an interval.         |
| `clearInterval(intervalId)` | Cancel a running interval.                        |

Learn more: [`references/sleep.md`](references/sleep.md),
[`references/set-timeout.md`](references/set-timeout.md), and
[`references/set-interval.md`](references/set-interval.md).

### Collection Runner Flow Control

Use these APIs to choose which request runs next while a collection is running
serially in Collection Runner.

| API                                        | Description                                               |
| ------------------------------------------ | --------------------------------------------------------- |
| `$.setNextRequest(requestIdOrTitle)`       | Set the next request by id or title.                      |
| `pm.setNextRequest(requestIdOrTitle)`      | Postman-compatible alias for `$.setNextRequest()`.        |
| `postman.setNextRequest(requestIdOrTitle)` | Legacy Postman-compatible alias for `$.setNextRequest()`. |

Learn more: [`references/set-next-request.md`](references/set-next-request.md).

## How to Use

Use the "Quick Reference" section above to understand the APIs and capabilities
of Testfully's scripting environment. Read individual API documentation for
detailed explanations, examples, and best practices for using each API
effectively in your scripts.

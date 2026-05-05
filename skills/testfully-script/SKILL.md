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
references/request.md
references/response.md
references/response-cookies.md
references/set-next-request.md
references/send-request.md
```

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

## How to Use

Use the "Quick Reference" section above to understand the APIs and capabilities
of Testfully's scripting environment. Read individual API documentation for
detailed explanations, examples, and best practices for using each API
effectively in your scripts.

```
references/console.md
references/request.md
references/send-request.md
```

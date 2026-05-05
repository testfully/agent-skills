# Set Next Request

Use `$.setNextRequest()` to control which request runs next in Collection
Runner. This API is useful for conditional branching, stopping a run early, or
skipping over requests that are not needed for the current data or response.

This feature is only available when requests are executed through Collection
Runner. It controls the next request in the runner sequence; it does not change
the behavior of sending a single request outside Collection Runner.

## APIs

| API                                        | Description                      |
| ------------------------------------------ | -------------------------------- |
| `$.setNextRequest(requestIdOrTitle)`       | Preferred Testfully API.         |
| `pm.setNextRequest(requestIdOrTitle)`      | Postman-compatible alias.        |
| `postman.setNextRequest(requestIdOrTitle)` | Legacy Postman-compatible alias. |

Prefer `$.setNextRequest()` for new Testfully scripts. Use `pm.setNextRequest()`
or `postman.setNextRequest()` when migrating Postman scripts.

## Parameters

### `requestIdOrTitle`

The `requestIdOrTitle` parameter must be a string that identifies the request to
execute next. Testfully matches the value against requests in the current
Collection Runner run using:

- request id
- request title

Use a unique request title when referencing requests by title. If more than one
request matches the provided value, the run is terminated because Testfully
cannot choose safely.

```javascript
$.setNextRequest("Refresh Access Token");
```

### Empty Value

Calling `$.setNextRequest()` without an argument, or passing an empty string,
stops the remaining requests in the current Collection Runner run.

```javascript
$.setNextRequest("");
```

Use this when the current request determines that no more requests should be
executed.

### Invalid or Missing Target

If the provided request id or title cannot be found in the current Collection
Runner run, Testfully terminates execution. The target must be one of the
original requests selected for the run.

```javascript
$.setNextRequest("A request title that is part of this runner run");
```

## Behavior

- When `$.setNextRequest()` is not called, Collection Runner continues with the
  next request in order.
- When `$.setNextRequest("Some Request")` is called, Collection Runner executes
  the matching request next.
- When the target request is later in the run, requests between the current
  request and the target are skipped.
- When `$.setNextRequest("")` or `$.setNextRequest()` is called, Collection
  Runner stops executing remaining requests.
- The API works in serial Collection Runner execution, where there is a clear
  next request to choose.

## Examples

### Branch Based on Response Status

Use this in an After Response script to send failures to a recovery request.

```javascript
if ($.response.code === 401) {
  $.setNextRequest("Refresh Access Token");
} else {
  $.setNextRequest("Get User Profile");
}
```

### Stop a Run Early

Use this in an After Response script when the current response shows the rest of
the run is unnecessary.

```javascript
if ($.response.code === 204) {
  console.info("No content returned; stopping remaining requests.");
  $.setNextRequest("");
}
```

### Skip Optional Requests Using Iteration Data

Use this in a Before Request script to branch based on Collection Runner data.

```javascript
const runBillingFlow = $.iterationData.get("run_billing_flow");

if (runBillingFlow !== "true") {
  $.setNextRequest("Final Verification");
}
```

### Retry a Request Once

Use this pattern carefully to avoid loops. Store retry state in a variable, then
route back to the current request or forward to the next request.

```javascript
const alreadyRetried = $.environment.get("profile_retry_done") === "true";

if ($.response.code >= 500 && !alreadyRetried) {
  $.environment.set("profile_retry_done", "true");
  $.setNextRequest("Get User Profile");
} else {
  $.environment.unset("profile_retry_done");
  $.setNextRequest("Final Verification");
}
```

### Use the Postman-Compatible Alias

```javascript
pm.setNextRequest("Final Verification");
```

## Guidance

- Use this API only in Collection Runner workflows.
- Prefer stable request ids or unique request titles.
- Avoid ambiguous titles; duplicate matches terminate execution.
- Use `$.setNextRequest("")` intentionally to stop the run early.
- Be careful when routing to an earlier request. Guard loops with state stored
  in an environment, collection, folder, or iteration variable.
- Keep branching logic close to the request that makes the decision, usually in
  an After Response script.

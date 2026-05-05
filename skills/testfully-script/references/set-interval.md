# setInterval

Use `setInterval(callback, ms)` to schedule a callback that runs repeatedly.
Always clear intervals when they are no longer needed.

## APIs

| API                         | Description                |
| --------------------------- | -------------------------- |
| `setInterval(callback, ms)` | Run a callback repeatedly. |
| `clearInterval(intervalId)` | Cancel a running interval. |

## Parameters

### `callback`

`callback` is required. Use a function callback for new scripts.

```javascript
const intervalId = setInterval(() => {
  console.log("tick");
}, 1000);
```

The sandbox also accepts string callbacks, but function callbacks are clearer
and should be preferred.

### `ms`

`ms` is the interval delay in milliseconds. It must be greater than `0`.
Testfully throws an error when the interval is missing, `0`, or negative.

```javascript
const intervalId = setInterval(() => {
  console.log("Every second");
}, 1000);
```

## Return Value

`setInterval()` returns a numeric interval id. Pass this id to `clearInterval()`
to stop the interval.

```javascript
const intervalId = setInterval(() => {
  console.log("Polling");
}, 1000);

clearInterval(intervalId);
```

## Behavior

- `setInterval()` requires at least one argument.
- The interval delay must be greater than `0`.
- The callback repeats until `clearInterval(intervalId)` is called or the
  sandbox is disposed.
- Testfully tracks running intervals while the sandbox is running.
- Errors thrown by interval callbacks are logged as error-level console entries.
- Calling `clearInterval()` without an id does nothing.

## Examples

### Run a Limited Number of Ticks

```javascript
let ticks = 0;

const intervalId = setInterval(() => {
  ticks += 1;
  console.log("tick", ticks);

  if (ticks >= 3) {
    clearInterval(intervalId);
  }
}, 500);
```

### Poll a Helper Endpoint Briefly

```javascript
let attempts = 0;

const intervalId = setInterval(() => {
  attempts += 1;

  const response = $.sendRequest("https://httpbin.org/anything");
  console.log("Poll status:", response.code);

  if (response.code === 200 || attempts >= 3) {
    clearInterval(intervalId);
  }
}, 1000);
```

## Guidance

- Prefer `$.sleep(ms)` or `setTimeout()` unless repeated execution is needed.
- Always call `clearInterval(intervalId)` when the work is complete.
- Guard intervals with a maximum attempt count to avoid accidental loops.
- Keep interval callbacks small; long-running callbacks can make scripts harder
  to reason about.

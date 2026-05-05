# setTimeout

Use `setTimeout(callback, ms)` to schedule a callback that runs once after a
delay. For simple sequential waits, prefer `$.sleep(ms)`.

## APIs

| API                        | Description                |
| -------------------------- | -------------------------- |
| `setTimeout(callback, ms)` | Run a callback once later. |
| `clearTimeout(timeoutId)`  | Cancel a pending timeout.  |

## Parameters

### `callback`

`callback` is required. Use a function callback for new scripts.

```javascript
setTimeout(() => {
  console.log("Delayed log");
}, 1000);
```

The sandbox also accepts string callbacks, but function callbacks are clearer
and should be preferred.

### `ms`

`ms` is the delay in milliseconds. If omitted, Testfully schedules the callback
with a `0` millisecond delay.

```javascript
setTimeout(() => {
  console.log("Runs soon");
});
```

## Return Value

`setTimeout()` returns a numeric timeout id. Pass this id to `clearTimeout()` to
cancel the timeout before it runs.

```javascript
const timeoutId = setTimeout(() => {
  console.log("This will not run");
}, 1000);

clearTimeout(timeoutId);
```

## Behavior

- `setTimeout()` requires at least one argument.
- The callback runs once.
- Testfully tracks pending timeouts while the sandbox is running.
- If the timeout callback throws, Testfully records the error for the sandbox
  execution.
- Calling `clearTimeout()` without an id logs a warning.

## Examples

### Delay a Diagnostic Log

```javascript
setTimeout(() => {
  console.log("Response time:", $.response.responseTime);
}, 250);
```

### Cancel a Timeout Conditionally

```javascript
const timeoutId = setTimeout(() => {
  console.warn("Slow path reached");
}, 1000);

if ($.response.code === 200) {
  clearTimeout(timeoutId);
}
```

## Guidance

- Prefer `$.sleep(ms)` for simple waits.
- Use `setTimeout()` when delayed callback behavior is clearer than sequential
  waiting.
- Store the returned id if the timeout might need to be cancelled.
- Keep callbacks small and deterministic.

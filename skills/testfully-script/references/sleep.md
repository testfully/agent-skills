# Sleep

Use `$.sleep(ms)` to pause script execution for a fixed amount of time. It is
the preferred Testfully API when a script needs a simple delay.

## API

| API           | Description                                   |
| ------------- | --------------------------------------------- |
| `$.sleep(ms)` | Wait for `ms` milliseconds before continuing. |

## Parameters

### `ms`

`ms` must be a positive number of milliseconds. Testfully throws an error when
the argument is missing, is not a number, or is less than or equal to `0`.

```javascript
$.sleep(1000);
console.log("Waited one second");
```

## Behavior

- `$.sleep(ms)` delays the current script before continuing.
- The delay is measured in milliseconds.
- Use `$.sleep()` for simple sequential waits instead of wrapping `setTimeout()`
  yourself.

## Examples

### Wait Before Sending a Helper Request

```javascript
$.sleep(500);

const response = $.sendRequest("https://httpbin.org/anything");
console.log("Helper status:", response.code);
```

### Wait Between Poll Attempts

```javascript
let status = "";

for (let attempt = 0; attempt < 3; attempt += 1) {
  const response = $.sendRequest("https://httpbin.org/anything");
  status = String(response.code);

  if (status === "200") {
    break;
  }

  $.sleep(1000);
}

console.log("Final status:", status);
```

## Guidance

- Prefer `$.sleep(ms)` for straightforward delays.
- Pass milliseconds, not seconds.
- Keep sleeps short; long sleeps make collection runs slower.
- Do not pass `0` or negative values.

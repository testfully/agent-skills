# npm Packages

Use standard JavaScript `import` syntax to load public npm packages in Testfully
scripts. Testfully resolves package imports at runtime, so you do not need to
install Node.js, npm, `node_modules`, or a local package manager for scripts to
use supported packages.

The imported package must work in a browser-style JavaScript sandbox. Packages
that require Node-specific APIs such as `fs`, `path`, `net`, `tls`,
`child_process`, `process`, or Node-only `crypto` behavior cannot be used unless
the package also ships a browser-compatible build that does not depend on those
APIs.

## Availability

Packages must be publicly available from npm so Testfully can resolve and
download them.

## Import Syntax

### Default Import

Use a default import when the package exports a default value.

```javascript
import _ from "lodash";

const result = _.merge({ name: "Teddy" }, { location: "AUS" });
console.log(result);
```

### Named Import

Use named imports when the package exposes named exports.

```javascript
import { nanoid } from "nanoid";

const requestId = nanoid();
$.request.headers.upsert({
  key: "X-Request-ID",
  value: requestId,
});
```

### Versioned Import

Pin a package version by including it in the package specifier.

```javascript
import _ from "lodash@4.17.21";

console.log(_.camelCase("Testfully Scripts"));
```

### Scoped Package Import

Scoped packages can be imported with their normal npm package name.

```javascript
import { z } from "zod";

const schema = z.object({
  code: z.number(),
});

schema.parse({ code: $.response.code });
```

## Runtime Behavior

- Testfully executes scripts as ES modules.
- Imports are resolved by Testfully at runtime from public npm packages.
- Dependencies of imported packages are resolved automatically when they are
  compatible with the sandbox.
- When caching is available, Testfully stores downloaded module code for reuse.
- Local `package.json`, `node_modules`, npm, and Node.js are not required for
  Testfully scripts.

## Browser-Compatible Packages

Choose packages that work in browser or web-worker environments. Good candidates
are pure JavaScript utilities, validation libraries, date libraries, encoding
helpers, and packages that publish ESM browser builds.

Avoid packages that depend on Node-only capabilities:

- filesystem access, such as `fs`
- operating system paths, such as `path`
- processes or shell commands, such as `child_process`
- raw network sockets, such as `net` or `tls`
- Node globals, such as `process`, `Buffer`, or `__dirname`
- Node-specific crypto behavior

If a package documents separate Node and browser entry points, use the package
only when the browser entry point supports the feature you need.

## Examples

### Merge Data with `lodash`

```javascript
import _ from "lodash";

const defaults = {
  retries: 1,
  baseUrl: "https://httpbin.org",
};

const config = _.merge(defaults, $.environment.toObject());
console.log("Config:", config);
```

### Validate Response JSON with `zod`

```javascript
import { z } from "zod";

const responseSchema = z.object({
  url: z.string(),
  method: z.string(),
});

const data = $.response.json();
const parsed = responseSchema.parse(data);
console.log("Echoed request:", parsed.method, parsed.url);
```

### Generate an ID with `nanoid`

```javascript
import { nanoid } from "nanoid";

$.request.headers.upsert({
  key: "X-Correlation-ID",
  value: nanoid(),
});
```

### Store Structured Data

```javascript
import _ from "lodash";

const summary = _.pick($.response.json(), ["url", "method"]);
$.environment.set("last_httpbin_summary", JSON.stringify(summary));
```

## Troubleshooting

### Package Not Found

If a package cannot be found, confirm that the package name is public and
spelled exactly as it appears on npm.

```javascript
import _ from "lodash";
```

### Package Uses Node APIs

If a package fails because it expects Node APIs, choose a browser-compatible
alternative. For example, prefer browser-ready utility libraries over packages
that read files, spawn processes, open sockets, or depend on Node internals.

### Import Location

Place imports at the top level of the script. Testfully tracks static import
statements so module-loading errors can point back to the script line that
requested the package.

```javascript
import _ from "lodash";

console.log(_.kebabCase("Import Location"));
```

## Guidance

- Prefer small, browser-compatible packages.
- Pin versions when reproducibility matters.
- Do not expect local npm dependencies or `node_modules` to be available.
- Do not use packages that require Node-only APIs.
- Keep imports at the top of the script for clarity and better error location.
- For simple tasks, prefer built-in JavaScript APIs before adding a package.

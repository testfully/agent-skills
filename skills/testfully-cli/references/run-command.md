# `run` Command

`testfully run` executes requests from one or more input paths. The command is
also the default command, so `testfully collection.json` is equivalent to
`testfully run collection.json`.

## Command Forms

```bash
testfully run [options] <input-path...>
testfully [options] <input-path...>
```

Input paths are mandatory. Supported input files include `.json` and `.toml`
files. Directories are also accepted for TFML workspaces or collections.

Use `--` before positional input paths when an option accepts one or more values
and appears before the input path.

### Usage examples

To run a collection file called `smoke_tests.json`, run the command below:

```bash
testfully run smoke_tests.json
```

For a request collection stored in a directory called `integration_tests`, with
shared global variables in `globals.toml`, put `--` before the directory path
because `--globals` accepts one or more values:

```bash
testfully run --globals globals.toml -- integration_tests
```

When `ssl-tests.json` needs an extra root CA certificate from `rootCA.crt`, put
`--` before the test file because the certificate option accepts one or more
values:

```bash
testfully run --ssl-extra-ca-certs rootCA.crt -- ssl-tests.json
```

## Arguments

### `<input-path...>`

Mandatory. One or more collection or workspace paths to run. Paths can be
`.json` files, `.toml` files, or directories containing supported Testfully
inputs. Examples include Testfully exports, Postman collection JSON files, TFML
files, and TFML workspace or collection directories.

#### Usage examples

After exporting a Testfully collection into `collection.json`, run every request
in that file with:

```bash
testfully run ./collection.json
```

### `--request-id`

Optional, repeatable string argument. Runs only the requests that match the
supplied request IDs. Use this when a collection contains many requests but only
specific requests should run.

#### Usage examples

If `collection.json` contains many requests and only request IDs `123` and `456`
should run:

```bash
testfully run --request-id 123 --request-id 456 -- collection.json
```

### `--folder-id`

Optional, repeatable string argument. Runs only requests in the supplied folder
IDs. Use this to run a subset of a collection by folder.

#### Usage examples

For a collection with a folder ID named `smoke`, run only the requests in that
folder with:

```bash
testfully run --folder-id smoke -- collection.json
```

### `--global-var`

Optional, repeatable string argument. Adds or overrides a global variable before
execution. Use the `name=value` format. Variable names are case-sensitive, and
later values override earlier values with the same name. Quote the value when it
contains spaces or shell-sensitive characters.

#### Usage examples

In CI, provide run-specific values for `api_key` and `base_url` without editing
`collection.json`:

```bash
testfully run \
  --global-var api_key=12345 \
  --global-var "base_url=https://api.example.com" \
  -- collection.json
```

### `--globals`, `-g`

Optional, repeatable string argument. Loads one or more globals files before
running the collection. Use this for shared workspace variables or CI-specific
global values. Use `--` before the collection path when the collection path
follows this option.

#### Usage examples

To run the `rick_and_morty_api` collection while loading shared globals from
`globals.toml`:

```bash
testfully run --globals globals.toml -- rick_and_morty_api
```

### `--environment-id`

Optional string argument. Selects an environment by ID from the loaded input
data. Use this when the collection export already includes the environment you
want to run with.

#### Usage examples

When `collection.json` includes an environment with ID `5164133`, make all
requests use that environment with:

```bash
testfully run --environment-id 5164133 collection.json
```

### `--environment`, `-e`

Optional string argument. Loads an environment file and uses it for the run.
When the environment file contains multiple environments, Testfully CLI uses the
first environment. When both `--environment` and `--environment-id` are
supplied, `--environment` takes precedence.

#### Usage examples

For a TFML workspace in `workspace` with local environment variables stored in
`workspace/environments/local.toml`:

```bash
testfully run --environment ./workspace/environments/local.toml ./workspace
```

### `--iteration-data`, `-d`

Optional, repeatable string argument. Supplies data for data-driven testing.
Each data row or object provides variables for an iteration. JSON files can
contain either an array of objects or a single object. Multiple iteration-data
files can be supplied and are combined for the run.

#### Usage examples

With test data rows in `data.json`, run the collection three times using that
data:

```bash
testfully run \
  --iteration-data ./data.json \
  --iteration-count 3 \
  ./payment_regression_tests
```

### `--iteration-count`, `-n`

Optional number argument. Sets how many times to run the collection. If omitted,
Testfully CLI runs once when no iteration data is provided, or once per supplied
iteration-data row when iteration data is provided. When the count is greater
than the number of data rows, data rows are reused.

#### Usage examples

To repeat the same smoke test suite five times without supplying an iteration
data file:

```bash
testfully run --iteration-count 5 ./smoke_tests
```

### `--delay-request`

Optional number argument. Sets the delay between requests in milliseconds. Use
this to slow down a run when testing rate-limited or timing-sensitive APIs.

#### Usage examples

For a rate-limited API, wait one second between requests in `collection.json`:

```bash
testfully run --delay-request 1000 collection.json
```

### `--memory-size`

Optional number argument. Sets the script execution memory limit in bytes. Use
this when scripts need more memory than the default runtime allocation.

#### Usage examples

When scripts in `collection.json` need 1 MB of memory:

```bash
testfully run --memory-size 1048576 collection.json
```

### `--verbose`

Optional boolean flag. Prints detailed request and response information,
including request method and URL, headers, payloads, response headers, response
body, and logs when present.

#### Usage examples

While debugging a failing request, inspect request and response details during
the `collection.json` run:

```bash
testfully run --verbose collection.json
```

### `--logs`

Optional boolean flag. Prints script console logs without enabling the full
verbose request and response output.

#### Usage examples

If collection scripts use console logs for debugging, show those logs without
full verbose output:

```bash
testfully run --logs collection.json
```

### `--silent`

Optional boolean flag. Suppresses CLI output for quieter automation runs.

#### Usage examples

For a CI job that should run `collection.json` with minimal terminal output:

```bash
testfully run --silent collection.json
```

### `--bail`

Optional boolean flag. Stops the run after the first errored request. Use this
in CI/CD pipelines when later requests should not run after an error.

#### Usage examples

When later requests depend on earlier setup requests, stop the run as soon as a
request errors:

```bash
testfully run --bail collection.json
```

### `--reporters`

Optional, repeatable string argument. Selects report output formats. Use `junit`
to generate a JUnit XML report for CI/CD systems. When omitted, Testfully CLI
prints the standard CLI report.

#### Usage examples

For CI systems that expect a JUnit report at `reports/junit.xml` after running
`collection.json`:

```bash
testfully run \
  --reporters junit \
  --reporter-junit-export ./reports/junit.xml \
  collection.json
```

### `--reporter-junit-export`

Optional string argument. Sets the JUnit report output path when the JUnit
reporter is enabled. The default output is `junit.xml`. If a directory path is
supplied, Testfully CLI writes `junit.xml` inside that directory.

#### Usage examples

To have Testfully CLI create `junit.xml` inside a directory called `reports`:

```bash
testfully run --reporters junit --reporter-junit-export ./reports collection.json
```

### `--reporter-cli-silent`

Optional boolean flag. Suppresses CLI reporter output. Use this when a command
should run quietly while still allowing other configured reporters, such as
JUnit, to write their outputs.

#### Usage examples

To suppress CLI reporter output while running `collection.json`:

```bash
testfully run --reporter-cli-silent collection.json
```

### `--reporter-cli-no-summary`

Optional boolean flag. Disables the final CLI statistics summary.

#### Usage examples

When request-level output is useful but the final statistics summary is not:

```bash
testfully run --reporter-cli-no-summary collection.json
```

### `--reporter-cli-no-failures`

Optional boolean flag. Disables the final CLI failures or errors summary table.

#### Usage examples

If another system already captures failures, hide Testfully CLI's final failures
table:

```bash
testfully run --reporter-cli-no-failures collection.json
```

### `--reporter-cli-no-assertions`

Optional boolean flag. Hides assertion output in the CLI reporter for each
request.

#### Usage examples

For a collection with many assertions, hide all per-request assertion lines for
a cleaner run:

```bash
testfully run --reporter-cli-no-assertions collection.json
```

### `--reporter-cli-no-success-assertions`

Optional boolean flag. Hides successful assertions while still showing failed
and pending assertions.

#### Usage examples

To focus on assertions that need attention, hide successful assertions:

```bash
testfully run --reporter-cli-no-success-assertions collection.json
```

### `--reporter-cli-no-console`

Optional boolean flag. Hides script console log output in the CLI reporter.

#### Usage examples

If logs were enabled for troubleshooting but script console output should stay
hidden:

```bash
testfully run --logs --reporter-cli-no-console collection.json
```

### `--reporter-cli-show-timestamps`

Optional boolean flag. Shows request timestamps in the CLI reporter.

#### Usage examples

To show when each request was sent during a run:

```bash
testfully run --reporter-cli-show-timestamps collection.json
```

### `--ssl-extra-ca-certs`

Optional, repeatable string argument. Adds extra CA certificate files for
requests that use certificates from a private or self-signed certificate
authority.

#### Usage examples

When `ssl-tests.json` calls an API secured by a private certificate authority,
provide the CA certificate from `rootCA.crt`:

```bash
testfully run --ssl-extra-ca-certs ./rootCA.crt -- ssl-tests.json
```

### `--ssl-client-cert`

Optional, repeatable string argument. Supplies client certificate files for
mutual TLS. Pair this with `--ssl-client-key` when using separate certificate
and key files.

#### Usage examples

For an API in `mtls.json` that requires a client certificate for
`dummy-api.example.com:9443`, with the certificate and key stored separately:

```bash
testfully run \
  --ssl-client-cert ./client.crt \
  --ssl-client-key ./client.key \
  --ssl-client-host dummy-api.example.com:9443 \
  -- mtls.json
```

### `--ssl-client-key`

Optional, repeatable string argument. Supplies client key files for mutual TLS.
Use it with `--ssl-client-cert` when the certificate and key are stored
separately.

#### Usage examples

When the server requires mutual TLS, provide both `client.crt` and `client.key`
while running `mtls.json`:

```bash
testfully run \
  --ssl-client-cert ./client.crt \
  --ssl-client-key ./client.key \
  -- mtls.json
```

### `--ssl-client-passphrase`

Optional, repeatable string argument. Supplies passphrases for
password-protected client certificates or keys.

#### Usage examples

For a password-protected client certificate stored in `client.pfx` with the
passphrase `secret`:

```bash
testfully run \
  --ssl-client-cert ./client.pfx \
  --ssl-client-passphrase secret \
  -- mtls.json
```

### `--ssl-client-host`

Optional, repeatable string argument. Associates a client certificate with a
host. Use this when different hosts require different client certificates.

#### Usage examples

If a run calls multiple hosts and this certificate should apply only to
`https://dummy-api.example.com:9443/echo`:

```bash
testfully run \
  --ssl-client-cert ./client.crt \
  --ssl-client-key ./client.key \
  --ssl-client-host https://dummy-api.example.com:9443/echo \
  -- mtls.json
```

### `--ssl-client-cert-list`

Optional, repeatable string argument. Loads a JSON file containing one or more
mutual TLS client certificate definitions. Use this when a run needs multiple
client certificates or host-specific certificate mappings.

#### Usage examples

When `mtls.json` needs certificate mappings from `ssl-client-cert-list.json`:

```bash
testfully run --ssl-client-cert-list ./ssl-client-cert-list.json -- mtls.json
```

The `ssl-client-cert-list.json` file would contain certificate mappings like
this:

```json
[
  {
    "name": "dummy-api",
    "matches": ["dummy-api.example.com:9443"],
    "key": { "src": "./client.key" },
    "cert": { "src": "./client.crt" },
    "passphrase": ""
  }
]
```

### `--insecure`, `-k`

Optional boolean flag. Disables SSL certificate verification. Use this only for
trusted local, test, or controlled environments.

#### Usage examples

For a trusted local or test API in `ssl-tests.json` whose certificate cannot be
verified normally:

```bash
testfully run --insecure ssl-tests.json
testfully run -k ssl-tests.json
```

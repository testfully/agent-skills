# Globals File

Use the `globals.toml` Globals file to define global variables that can be used
across all requests in the workspace. Remember, a `globals.toml` file is not
required for a workspace; it's only needed if you want to define global
variables.

## Detection

- Testfully Desktop looks for a `globals.toml` file directly under the project
  root. If found, it will be loaded as the globals file.

- Testfully CLI looks for a `globals.toml` file in the project root, and also
  accepts it as a flag or argument for commands that support it.

## Define Global Variables

Global variables are defined under the `[variables]` section in the globals
file. Since `globals.toml` must remain sematically valid, you MUST only include
one `[variables]` section in the file. Should you need to have more than one
section of variables, you MUST use `[[variables]]` section instead.

Prefer `[variables]` for simplicity unless you have a specific need for multiple
sections.

A global variable is a key-value pair where the key is the variable name and the
value is the variable value. For example:

```toml
[variables]
base_url = "https://api.example.com"
client_id = "abc123"
```

Prefix a global variable with `-` to make it unavailable for use in requests.
This is useful for temporarily disabling a variable without deleting it. For
example:

```toml
[variables]
-base_url = "https://api.example.com"
client_id = "abc123"
```

Should you need to document a global variable, you can add a comment above it.
For example:

```toml
[variables]
base_url = "https://api.example.com" # The base URL for all API requests
client_id = "abc123" # The client ID for authentication
```

A `globals.toml` file DOES NOT carry any other configuration or metadata fields
besides the documented `variables` field. YOU MUST NOT include any other fields
in the file, as they will be ignored.

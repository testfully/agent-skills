# Folder File

In Testfully, requests are organised using collections, folders and subfolders.

- A directory in filesystem under the `collections` directory represents a
  collection.
- A directory or subdirectory under a collection represents a folder or
  subfolder.

If a collection, folder, or subfolder is purely used for grouping, you do not
need to author a companion file for it. Instead, create a directory with the
appropriate name and let it be auto-detected by Testfully.

However, a collection, folder, or subfolder can carry additional configuration
and metadata to enhance the behaviour of the requests within it. For example:

- You can define one or more variables at any level (collection, folder, or
  subfolder) and use those variables in any requests within the same level or
  nested levels.

- You can specify common headers and query parameters at the folder level to
  avoid repeating them in each request.

Below are the fields you can use when authoring a folder file.

## Fields

| Field            | Type                | Required | Description                                                       |
| ---------------- | ------------------- | -------- | ----------------------------------------------------------------- |
| `name`           | `string`            | No       | Folder name. If missing, the file name is used.                   |
| `seq`            | `number`            | No       | Sequence order.                                                   |
| `execution_mode` | `string`            | No       | `not_set`, `parallel`, `random`, or `serial`.                     |
| `pre_request`    | `string`            | No       | Script executed before the folder runs.                           |
| `post_response`  | `string`            | No       | Script executed after the folder runs.                            |
| `timeout`        | `number`            | No       | Maximum run time in milliseconds. Omit or use `null` for default. |
| `delay`          | `number`            | No       | Delay in milliseconds before starting the folder.                 |
| `secure`         | `boolean`           | No       | SSL verification flag.                                            |
| `insecure`       | `boolean`           | No       | Inverse SSL verification flag.                                    |
| `redirects`      | `boolean \| number` | No       | Redirect behavior.                                                |
| `history`        | `string`            | No       | History behavior.                                                 |
| `headers`        | `Header[]`          | No       | Folder headers.                                                   |
| `variables`      | `Variable[]`        | No       | Folder variables.                                                 |
| `params`         | `Param[]`           | No       | Folder query parameters.                                          |
| `proxy`          | `ProxyFile`         | No       | Proxy configuration.                                              |

In addition to the above fields, folder files also accept any of the fields
supported by the following objects:

- [Authorization](./authorization.md)

## Example

Let's say we have a bunch of API requests to `https://httpbin.org` that we want
to organise under a collection called `HTTPBin`. Also, we want to be neat and
avoid repeating the base URL. We can create a folder file with the following
content:

```toml
name = "HTTPBin"
baseUrl = "https://httpbin.org"
```

We can then place our request files under the same directory as the folder file,
and they will inherit the `baseUrl` variable defined at the folder level. For
example, a request file named `get-ip.toml` with the following content:

```toml
method = "POST"
url = "${baseUrl}/anything"
json = """
{
  "message": "Hello, HTTPBin!"
}
"""
```

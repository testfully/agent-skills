# CookieJar File

Cookie files are detected only when inside a `cookies/` directory. Testfully
uses a cookie jar file that groups cookies for a domain. When authoring a
cookiejar file, use the name of the file for the domain that the cookies belong
to. For example, `cookies/httpbin.org.toml` would be the cookie jar for
`httpbin.org` and could contain cookies for that domain ONLY.

## CookieJar Fields

A cookie jar file contains one or more cookies, represented using the
`[[cookie]]` field in TOML.

| Field    | Type           | Required | Description                                                     |
| -------- | -------------- | -------- | --------------------------------------------------------------- |
| `cookie` | `CookieFile[]` | No       | Cookies contained in this jar. TOML uses singular `[[cookie]]`. |

## Cookie fields

Each of the following fields can be used when defining a cookie in a cookie jar
file. All fields are optional, but at least one of `name`, `value`, or `raw`
should be provided to avoid creating an empty cookie.

| Field         | Type      | Required | Description                            |
| ------------- | --------- | -------- | -------------------------------------- |
| `name`        | `string`  | No       | Cookie name.                           |
| `value`       | `string`  | No       | Cookie value.                          |
| `path`        | `string`  | No       | Cookie path.                           |
| `expires`     | `string`  | No       | ISO timestamp.                         |
| `max_age`     | `number`  | No       | Numeric max-age in seconds.            |
| `http_only`   | `boolean` | No       | HTTP-only flag.                        |
| `same_site`   | `string`  | No       | `lax`, `none`, or `strict`.            |
| `secure`      | `boolean` | No       | Secure cookie flag.                    |
| `included`    | `boolean` | No       | Whether the cookie is included.        |
| `partitioned` | `boolean` | No       | Partitioned cookie flag.               |
| `raw`         | `string`  | No       | Raw `Set-Cookie` string, if available. |

## Example

Below is an example of a cookie jar file with one cookie defined. It simply
defines a cookie named `session_id` with a value of `httpbin-session-id-12345`.
The cookie will be included in requests to `httpbin.org` and its subpaths, but
it will not be sent to other domains or to `httpbin.org` subdomains (e.g.
`api.httpbin.org`) since the cookie jar is only associated with `httpbin.org`.

```toml
[[cookie]]
name = "session_id"
value = "httpbin-session-id-12345"
```

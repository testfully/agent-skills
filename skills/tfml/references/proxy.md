# Proxy

In context of Testfully, proxy settings are used for two primary purposes:

1. A developer operating in an highly secure environment may need to route
   outgoing HTTP requests through a corporate proxy server to comply with
   network policies and have access to internet resources.

2. A developer may want to debug and inspect outgoing HTTP requests from
   Testfully, hence routing requests through a proxy server that captures and
   logs them using a tool.

As such, a `proxy` field can be attached to a collection, folder, or sub-folder
file to configure proxy settings for all requests within that scope.

## `proxy=false`

Use this setting to explicitly disable proxy settings for the associated scope.
You would use this if proxy settings are enabled at a higher level (e.g.
collection) but you want to disable them for a specific folder or sub-folder.

## `proxy="env"`

Use this setting to instruct Testfully to read proxy settings from environment
variables.

## `proxy=url`

Provide a URL string to specify a custom proxy server. You would use this if you
want to route requests through a specific proxy server and the only thing you
need to configure is the URL (including protocol, host, and optional port).

## `proxy` object

Use the `[proxy]` section when you need to supply username/password for
authorization, need to bypass some URLs.

The following fields are supported in the `[proxy]` section.

| Field      | Type      | Required | Description                                     |
| ---------- | --------- | -------- | ----------------------------------------------- |
| `bypass`   | `string`  | No       | Bypass rules.                                   |
| `host`     | `string`  | No       | Proxy URL or host. URL format is preferred.     |
| `http`     | `boolean` | No       | Whether to intercept HTTP. Omitted means true.  |
| `https`    | `boolean` | No       | Whether to intercept HTTPS. Omitted means true. |
| `username` | `string`  | No       | Proxy username.                                 |
| `password` | `string`  | No       | Proxy password.                                 |

## Examples

To use proxy settings from environment variables, we can use the following
configuration in a folder file:

```toml
proxy = "env"
```

To disable proxy settings for a specific folder, we can use:

```toml
proxy = false
```

To point to a specific proxy server using the URL, we can use:

```toml
proxy = "http://proxy.example.com:8080"
```

To bypass `localhost` and `127.0.0.1` when routing through a proxy server, we
can use the following configuration in a folder file:

```toml
[proxy]
host = "proxy.example.com"
bypass = "localhost,127.0.0.1"
https = true
```

To authorize with a proxy server using basic authentication, we can use the
following configuration in a folder file:

```toml
[proxy]
host = "http://proxy.example.com:8080"
username = "proxyuser"
password = "proxypass"
```

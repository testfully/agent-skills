# Authorization

Authorization fields are embedded at the root of folder files, environment
files, and request step objects. Only one method should usually be populated.
When multiple methods are present, Testfully detects the active type by this
priority:

1. `no_auth`
2. `basic_auth`
3. `bearer_token`
4. `api_key`
5. `oauth2`
6. `oauth1`
7. `akamai_edge_grid`
8. `ntlm`
9. `aws_signature`

## Root Fields

Add any of the following fields to a supported file to enable authentication for
that file using the specified method or use the `no_auth` to explicitly disable
authentication. The `no_auth` is useful to stop inheritance mechanisms from
being kicked in.

| Field              | Type                                 | Required | Description                                                  |
| ------------------ | ------------------------------------ | -------- | ------------------------------------------------------------ |
| `basic_auth`       | `BasicAuthorization`                 | No       | Basic authentication settings.                               |
| `bearer_token`     | `string \| BearerTokenAuthorization` | No       | Bearer token shorthand or full settings.                     |
| `api_key`          | `APIKeyAuthorization`                | No       | API key authentication settings.                             |
| `oauth2`           | `OAuth2Authorization`                | No       | OAuth2 authentication settings.                              |
| `oauth1`           | `OAuth1Authorization`                | No       | OAuth1 authentication settings.                              |
| `akamai_edge_grid` | `AkamaiEdgeGridAuthorization`        | No       | Akamai EdgeGrid settings.                                    |
| `ntlm`             | `NTLMAuthorization`                  | No       | NTLM authentication settings.                                |
| `aws_signature`    | `AWSSignatureAuthorization`          | No       | AWS Signature V4 settings.                                   |
| `no_auth`          | `boolean`                            | No       | Explicitly disables authentication and prevents inheritance. |

## BasicAuthorization

| Field      | Type     | Required |
| ---------- | -------- | -------- |
| `username` | `string` | No       |
| `password` | `string` | No       |
| `bearer`   | `string` | No       |
| `prefix`   | `string` | No       |

## BearerTokenAuthorization

Use a string for the common `Authorization: Bearer <token>` case:

```toml
bearer_token = "{{token}}"
```

Use an object when prefix or bearer placement differs:

| Field    | Type     | Required |
| -------- | -------- | -------- |
| `token`  | `string` | No       |
| `prefix` | `string` | No       |
| `bearer` | `string` | No       |

## APIKeyAuthorization

| Field         | Type     | Required | Notes                                                                   |
| ------------- | -------- | -------- | ----------------------------------------------------------------------- |
| `api_key`     | `string` | No       | API key value.                                                          |
| `bearer`      | `string` | No       | Header or query parameter name.                                         |
| `destination` | `string` | No       | `header` or `query_string`. Defaults to `header` if invalid or omitted. |

## OAuth2Authorization

| Field                        | Type     | Required | Allowed values                                                                                                    |
| ---------------------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------- |
| `provider`                   | `string` | No       | `auth0`, `microsoft_azure_ad`, `okta`, `other`                                                                    |
| `access_token_url`           | `string` | No       |                                                                                                                   |
| `authorization_url`          | `string` | No       |                                                                                                                   |
| `callback_url`               | `string` | No       |                                                                                                                   |
| `client_authentication_type` | `string` | No       | `basic_auth_header`, `body`                                                                                       |
| `client_id`                  | `string` | No       |                                                                                                                   |
| `grant_type`                 | `string` | No       | `authorization_code`, `authorization_code_with_pkce`, `client_credentials`, `implicit`, `resource_owner_password` |
| `client_secret`              | `string` | No       |                                                                                                                   |
| `scope`                      | `string` | No       |                                                                                                                   |
| `state`                      | `string` | No       |                                                                                                                   |
| `username`                   | `string` | No       |                                                                                                                   |
| `password`                   | `string` | No       |                                                                                                                   |
| `code_challenge_method`      | `string` | No       | `plain`, `sha_256`                                                                                                |
| `code_verifier`              | `string` | No       |                                                                                                                   |
| `audience`                   | `string` | No       |                                                                                                                   |
| `refresh_policy`             | `string` | No       | `per_request`, `reuse_till_expires`                                                                               |

## OAuth1Authorization

| Field                  | Type      | Required | Allowed values                                                                                 |
| ---------------------- | --------- | -------- | ---------------------------------------------------------------------------------------------- |
| `flow`                 | `string`  | No       | `three_legged`, `two_legged`                                                                   |
| `signature_method`     | `string`  | No       | `hmac_sha1`, `hmac_sha256`, `hmac_sha512`, `plaintext`, `rsa_sha1`, `rsa_sha256`, `rsa_sha512` |
| `consumer_key`         | `string`  | No       |                                                                                                |
| `consumer_secret`      | `string`  | No       |                                                                                                |
| `private_key`          | `string`  | No       |                                                                                                |
| `private_key_filename` | `string`  | No       |                                                                                                |
| `access_token`         | `string`  | No       |                                                                                                |
| `token_secret`         | `string`  | No       |                                                                                                |
| `request_token_url`    | `string`  | No       |                                                                                                |
| `request_token_method` | `string`  | No       | HTTP method                                                                                    |
| `authorise_url`        | `string`  | No       |                                                                                                |
| `access_token_url`     | `string`  | No       |                                                                                                |
| `access_token_method`  | `string`  | No       | HTTP method                                                                                    |
| `callback_url`         | `string`  | No       |                                                                                                |
| `verifier`             | `string`  | No       |                                                                                                |
| `timestamp`            | `string`  | No       |                                                                                                |
| `nonce`                | `string`  | No       |                                                                                                |
| `nonce_length`         | `number`  | No       |                                                                                                |
| `version`              | `string`  | No       |                                                                                                |
| `realm`                | `string`  | No       |                                                                                                |
| `include_body_hash`    | `boolean` | No       |                                                                                                |
| `empty_params`         | `boolean` | No       |                                                                                                |
| `encode_params`        | `boolean` | No       |                                                                                                |
| `last_ampersand`       | `boolean` | No       |                                                                                                |
| `parameter_separator`  | `string`  | No       |                                                                                                |
| `bearer`               | `string`  | No       | `header`, `body`, `derived`                                                                    |

## AkamaiEdgeGridAuthorization

| Field             | Type     | Required |
| ----------------- | -------- | -------- |
| `access_token`    | `string` | No       |
| `client_token`    | `string` | No       |
| `client_secret`   | `string` | No       |
| `nonce`           | `string` | No       |
| `timestamp`       | `string` | No       |
| `base_url`        | `string` | No       |
| `headers_to_sign` | `string` | No       |
| `max_body_size`   | `string` | No       |

## NTLMAuthorization

| Field                   | Type      | Required |
| ----------------------- | --------- | -------- |
| `username`              | `string`  | No       |
| `password`              | `string`  | No       |
| `domain`                | `string`  | No       |
| `workstation`           | `string`  | No       |
| `retry_request`         | `boolean` | No       |
| `ntlm_protocol_version` | `number`  | No       |

## AWSSignatureAuthorization

| Field               | Type     | Required | Notes                      |
| ------------------- | -------- | -------- | -------------------------- |
| `access_key_id`     | `string` | No       |                            |
| `secret_access_key` | `string` | No       |                            |
| `session_token`     | `string` | No       |                            |
| `region`            | `string` | No       |                            |
| `service`           | `string` | No       |                            |
| `destination`       | `string` | No       | `header` or `query_string` |

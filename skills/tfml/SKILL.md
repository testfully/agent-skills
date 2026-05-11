---
name: tfml
description:
  A skill for authoring TFML (Testfully Markup Language) files with the correct
  supported file kinds, schemas, field names, and shared object structures.
license: MIT
metadata:
  author: testfully
  version: "1.0.0"
---

# TFML Skill

Use this skill when creating or editing Testfully Markup Language (TFML) files
for file-based Testfully workspaces.

TFML files are structured data files that describe workspaces, folders,
requests, environments, globals, vaults, cookies, and certificates.

## When to Apply

Use and reference this skill and guidelines when:

- Authoring TFML files with `.toml` extension.

## File Types

TFML files come in various types, each with a specific purpose and expected
location within a Testfully workspace. Use the table beloe to identify the kind
of TFML file you are working with and refer to the corresponding schema for
details on its structure and fields.

| Kind        | Typical file or location                        | Schema URL                                      |
| ----------- | ----------------------------------------------- | ----------------------------------------------- |
| Workspace   | `workspace.toml`                                | `https://testfully.io/schemas/workspace.json`   |
| Globals     | `globals.toml`                                  | `https://testfully.io/schemas/global.json`      |
| Folder      | `index.toml` within the `collections` directory | `https://testfully.io/schemas/folder.json`      |
| Request     | request-shaped file                             | `https://testfully.io/schemas/request.json`     |
| Environment | inside `environments/`                          | `https://testfully.io/schemas/environment.json` |
| Vault       | inside `vaults/`                                | `https://testfully.io/schemas/vault.json`       |
| Certificate | inside `certificates/`                          | `https://testfully.io/schemas/certificate.json` |
| Cookie jar  | inside `cookies/`                               | `https://testfully.io/schemas/cookie-jar.json`  |

Use links below to learn about each file type's expected location, shape, and
schema:

- [references/workspace-file.md](references/workspace-file.md),
- [references/globals-file.md](references/globals-file.md),
- [references/folder-file.md](references/folder-file.md),
- [references/request-file.md](references/request-file.md),
- [references/environment-file.md](references/environment-file.md),
- [references/vault-file.md](references/vault-file.md),
- [references/certificate-file.md](references/certificate-file.md),
- [references/cookie-file.md](references/cookie-file.md).

### Shared Structures

Several TFML file types reuse the same object structures. Do not duplicate these
schemas in generated docs or output; use the shared definitions.

| Structure                                  | Used by                                       | Reference                                                      |
| ------------------------------------------ | --------------------------------------------- | -------------------------------------------------------------- |
| Authorization                              | Folder, request steps, environment            | [`references/authorization.md`](references/authorization.md)   |
| Proxy                                      | Folder                                        | [`references/proxy.md`](references/proxy.md)                   |
| Headers, params, variables, SSL, redirects | Folder, request steps, globals, environment   | [`references/common-objects.md`](references/common-objects.md) |
| Request step and payloads                  | Request root and multi-step `request` entries | [`references/request-file.md`](references/request-file.md)     |

## Authoring Rules

- Must be valid TOML file with `.toml` extension.
- Include `$schema` for clarity, even when file location can infer the kind.
- Use lowercase enum values in files, for example `get`, `serial`, `store`, and
  `internet`.
- Treat fields prefixed with `__` as internal/transient. Do not author them in
  user-created TFML files.
- Use `null` or omit a field to leave it unset. For enabled flags, omitted means
  enabled unless the specific reference says otherwise.
- For request bodies, set only one payload shape when possible. The runtime
  chooses request type by priority; conflicting payload fields can make intent
  unclear.

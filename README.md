<p align="center">
  <img src="assets/gray-logo.svg" alt="gray" width="96">
</p>
<h1 align="center">gray-include</h1>
<p align="center">`@path` includes for AGENTS.md — referenced files expand into prompt context.</p>
<p align="center">
  <a href="https://github.com/vstaln/gray-include/blob/main/LICENSE"><img alt="MIT License" src="https://img.shields.io/badge/license-MIT-blue.svg"></a>
  <img alt="gray plugin" src="https://img.shields.io/badge/gray-plugin-7aa2f7.svg">
  <img alt="rust" src="https://img.shields.io/badge/built%20with-rust-orange.svg">
</p>

`@path` includes for AGENTS.md / CLAUDE.md — referenced files are expanded
into prompt context automatically.

A sidecar plugin for [gray](https://github.com/vstaln/gray), scaffolded by
[gray-account](https://github.com/vstaln/gray-account).

## What it does

On every `prompt/context` request, gray-include reads `AGENTS.md` and
`CLAUDE.md` in the session directory, finds `@path/to/file` tokens
(line-level and inline — tokens must not be glued to a word char, so
`admin@x.md` is not a reference, and fenced code blocks are ignored), reads
the referenced files, and recursively expands includes inside included
files. Nested paths resolve relative to the including file.

- Depth cap: 8 levels
- Cycle-safe: canonical-path visited set
- Total output capped at ~40k chars with a truncation marker
- Missing files and binaries become `_not found_` / `_binary file_` notes
- Fail open: any unexpected error returns `{}` — no context, no crash

Injected text looks like:

```markdown
## Included context

### @docs/api.md
````
<file body>
````
```

`/include lint` prints every `@token` found in the context files and
whether it resolved (`ok → path`, `missing`, `binary`, `unreadable`).

## Wire

`prompt/context` (hook) + `command/run` for `/include`. Manifest claims
`commands: ["/include"]`, `hooks: ["prompt/context"]`, no tools,
protocol `1.1`.

## Install

```sh
gray plugin install include
```

## Develop

```sh
cargo test
gray account check      # entry point + manifest handshake
gray account publish    # check → build → release → publish to the gray registry
```

Bump `version` in `Cargo.toml` before each `publish`; the registry refuses to
republish a version.

---

Part of the [gray](https://github.com/vstaln/gray) plugin ecosystem —
the open-source AI agent harness. <https://gray.alignment.id>

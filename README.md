# php-lsp — Claude Code Plugin

A Claude Code LSP plugin that wires up [`php-lsp`](https://github.com/jorgsowa/php-lsp) — a full-featured PHP language server written in Rust — as the PHP language intelligence provider.

## Why php-lsp over phpactor?

`php-lsp` provides substantially richer language intelligence:

- **Semantic diagnostics** — undefined variables/functions/classes, arity errors, type errors, `@deprecated` warnings, duplicate declarations
- **Code actions** — add `use` import, generate constructor/getters/setters, implement missing methods, extract/inline variable, extract method, organize imports, add return type
- **Refactoring** — rename across all indexed files (functions, classes, variables, properties); file rename updates `use` statements
- **Navigation** — call hierarchy, type hierarchy, go-to-declaration, go-to-type-definition
- **Inlay hints** — parameter name labels, return-type labels
- **PHPUnit test runner** — "Run test" code lens on test methods
- **PSR-4/Composer** — resolves FQNs to files via `composer.json`; multi-root workspace support
- **PHPDoc** — `@param`, `@return`, `@var`, `@throws`, `@deprecated`, `@template`, `@mixin`, Psalm/PHPStan tag aliases

## Prerequisites

- Rust toolchain: https://rustup.rs

## Installation

**1. Install the `php-lsp` binary:**

```bash
cargo install php-lsp
```

**2. Add the plugin to Claude Code:**

```bash
claude plugin add https://github.com/jorgsowa/claude-php-lsp-plugin
```

## Configuration

`php-lsp` is configured via `initializationOptions`. The defaults in `.lsp.json` work for most projects. To override per-project, add to your LSP client config:

```json
{
  "phpVersion": "8.2",
  "excludePaths": ["cache/*", "storage/*"],
  "diagnostics": {
    "undefinedVariables": false
  }
}
```

### Options

| Option | Default | Description |
|--------|---------|-------------|
| `phpVersion` | `"8.1"` | PHP version for syntax and feature support |
| `excludePaths` | `[]` | Glob patterns for paths to skip during indexing |
| `diagnostics.enabled` | `true` | Master toggle for all diagnostics |
| `diagnostics.undefinedVariables` | `true` | Warn on undefined variables |
| `diagnostics.undefinedFunctions` | `true` | Warn on calls to undefined functions |
| `diagnostics.undefinedClasses` | `true` | Warn on undefined class references |
| `diagnostics.arityErrors` | `true` | Error on wrong argument count |
| `diagnostics.typeErrors` | `true` | Error on type mismatches |
| `diagnostics.deprecatedCalls` | `true` | Warn when calling `@deprecated` symbols |
| `diagnostics.duplicateDeclarations` | `true` | Error on duplicate class/function declarations |

## License

MIT

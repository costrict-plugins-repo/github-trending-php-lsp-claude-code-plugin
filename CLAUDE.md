# php-lsp Plugin — Claude Guidance

This project uses `php-lsp` as the PHP language server. Beyond the standard 5 LSP operations (go-to-definition, find-references, hover, document symbols, diagnostics), `php-lsp` exposes the following capabilities you can leverage.

## Diagnostics

`getDiagnostics` returns semantic errors in addition to parse errors:

- **Undefined variables** inside function/method bodies
- **Undefined functions and classes** across the workspace
- **Arity errors** — wrong number of arguments at call sites
- **Type errors** — literal type mismatches and null-safety violations
- **`@deprecated` warnings** — calling a function/method annotated with `@deprecated`
- **Duplicate declarations** — redefining a class, function, interface, trait, or enum

## Hover

`hover` returns rich information:

- Function/method signatures with `@param`/`@return`/`@throws` docblock annotations
- Variable type (`$var` → inferred class name)
- Property type (`$obj->prop` → `(property) ClassName::$prop: TypeHint`)
- Built-in class members (PDO, DateTime, Exception, etc.)
- `use` alias resolution (hovering an aliased name shows the FQN)
- Deprecated symbols show a `> Deprecated` banner
- Built-in PHP functions include a link to php.net documentation

## Code Actions

When you see a diagnostic or want to refactor, request code actions at the relevant position. Available actions:

| Action | Trigger |
|--------|---------|
| **Add use import** | Undefined class name |
| **Generate constructor** | Inside a class with declared properties |
| **Generate getters/setters** | Inside a class with declared properties |
| **Implement missing methods** | Class that extends abstract class or implements interface |
| **Extract variable** | Non-empty selection of an expression |
| **Extract method** | Multi-line selection inside a class method |
| **Inline variable** | Cursor on a variable assignment |
| **Add return type** | Function/method missing return type annotation |
| **Organize imports** | Any PHP file — sorts `use` statements, removes unused |
| **Add PHPDoc** | Undocumented function or method |

## Rename

`rename` works across all indexed files for:
- Functions, methods, classes, interfaces, traits, enums
- Variables and parameters (scoped to enclosing function/method)
- Properties (via `->` and `?->` across all files)
- Also updates `use` import statements

## Navigation

- **Call hierarchy** — incoming callers and outgoing callees for any function or method (cross-file)
- **Type hierarchy** — supertypes and subtypes for classes and interfaces
- **Go-to-declaration** — jump to the abstract/interface declaration of a method
- **Go-to-type-definition** — jump to the class of a variable's inferred type

## Inlay Hints

Request inlay hints to see:
- Parameter name labels at call sites (e.g. `foo(/*x:*/ 1, /*y:*/ 2)`)
- Return-type labels after function calls and closures

## Code Lens — PHPUnit Test Runner

Test methods in PHPUnit test classes have a "Run test" code lens. You can trigger the command `php-lsp.runTest` to run the test via `vendor/bin/phpunit --filter <TestName>`. The result is shown as a message with **Run Again** and **Open File** action buttons.

## Workspace Indexing

`php-lsp` indexes all `*.php` files in the workspace on startup (background, up to 50,000 files including `vendor/`). PSR-4 autoload maps are read from `composer.json` and `vendor/composer/installed.json`. After indexing completes, diagnostics, code lenses, and inlay hints are refreshed automatically.

Use `excludePaths` in `initializationOptions` to skip directories like `cache/*` or `storage/*`.

## PHPDoc Type System

`php-lsp` understands the full PHPDoc type system:

- `@param`, `@return`, `@var`, `@throws`, `@deprecated`, `@see`, `@link`
- `@template T` / `@template T of Base` generics
- `@mixin ClassName`
- `@psalm-type` / `@phpstan-type` type aliases
- `@property`, `@property-read`, `@property-write`, `@method [static]` — synthesised members appear in completions
- Psalm/PHPStan tag aliases (`@psalm-param`, `@phpstan-return`, etc.) are treated identically to standard tags

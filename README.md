# Wiz compiler

This repository contains the configuration system and compiler for the Wiz typed shell language. The compiler accepts `.wiz` and `.d.wiz` source, preserves shell-oriented syntax, performs binding and static type checking, lowers typed constructs, and emits executable shell with source maps.

Published packages:

- [`@wiz-sh/config`](https://www.npmjs.com/package/@wiz-sh/config) discovers, merges, validates, and normalizes `config.wiz.json`.
- [`@wiz-sh/compiler`](https://www.npmjs.com/package/@wiz-sh/compiler) exposes the stable parse, program, diagnostics, checking, lowering, emission, and source-map APIs.

## Pipeline

```text
source text → lexer → lossless syntax tree → AST → binder → checker
            → lowering → target backend → emitted shell + source map
```

The compiler is handwritten and deliberately keeps its phases in one repository so syntax, semantic, and backend changes can evolve together. Bash, Zsh, POSIX sh, Fish, PowerShell, and cmd targets share an explicit backend boundary; target-specific diagnostics reject constructs that cannot be preserved safely.

Wiz also models byte streams separately from shell strings. This makes null-delimited and binary workflows explicit instead of pretending shell variables can hold `NUL` bytes.

## API

```ts
import {
    checkProgram,
    createCompilerHost,
    createProgram,
    emitProgram,
    getDiagnostics,
    parseSourceFile,
} from "@wiz-sh/compiler";
```

Only stable entry points are exported. Formatter, linter, language service, and other consumers should not import private phase modules.

## Development

```console
bun install
bun run check
bun run build
```

The test suite includes lexer and parser recovery, binding, type checking, runtime-check insertion, target emission, source maps, `bash -n`, executable fixtures, and differential shell behavior. Linux and macOS CI additionally exercise installed shell targets.

See the [Wiz documentation](https://github.com/wiz-sh/docs) for language syntax and target behavior. Licensed under [MIT](LICENSE).

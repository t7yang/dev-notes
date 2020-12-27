# Summary of TypeScript Performance

This is a summary of official [TypeScript performance](https://github.com/microsoft/TypeScript/wiki/Performance).

### TL;DR

- Use `interface` over `type` for merging multiple types and a base types for serveral subtypes instead of a large union types.
- Annotate types especially for return types.
- Use project references for non-trivial size codebase.
- Setup `include` and `exclude` properly in `tsconfig` or `jsconfig`.
- Enabled flags that help speed up TypeScript: `incremental`, `skipLibCheck`, `strictFunctionType`, `isolatedModules`.
- If you working with 3rd party tools, read their performance suggestions carefully.
- Investigating issue with disabling editor plugin, run `tsc` with diagnotics and trace.
- Keep TypeScript up to date.

### Writing Easy-to-Compile Code

> Preferring Interfaces Over Intersections

Why:

When compose two or more types, extending types with `interface`s / extends is suggested, cause intersection types recursively merge properties while interface create a single flat object type.

> Using Type Annotations

Why:

Anonymous types by type inference may costly especially return types (export function).

> Preferring Base Types Over Unions

Why:

A large union types (for function parameter) can cause a real problems in compilation speed, an alternative is a base type with common members and serveral subtypes, e.g. `HtmlElemnt` and `DivElement ... ImgElement`.

### Using Project References

Why:

Split a non-trivial size codebase into serveral independent projects with project references can avoid loading too many files in single compilation and easier to organize difference layout strategies for certain codebase. ([example](https://dev.to/t7yang/typescript-yarn-workspace-monorepo-1pao))

### Configuring `tsconfig` or `jsconfig.json`

> Specifying Files

Why:

Exclude "heavy" folders like `node_modules` and include only needed folder may avoid walking through unnecessary folders which can slow down compilations. To avoid this, setup either `files` / `include` and `exclude`.

For best pratice, include only input source code folders, exclude any unrelated files and folder like artifacts, test, and dependency folder.

> Controlling `@types` Inclusion

Why:

TypeScript automatically includes every `@types` package in `node_modules`, this slow down both compilation and editing scenarios.

This can solve by specifying empty field for `types` and include types manually.

> Incremental Project Emit

Why:

`--incremental` flag allow TypeScript save last compilation state which help TypeScript re-check / re-emit the smallest set of files. Incremental is enabled by default when using `composite` flag for project references.

> Skipping `d.ts` Checking

Why:

TypeScript performs a full re-check of all `d.ts` files which typically unnecessary. `skipDefaultLibCheck` can skip type-checking of the `d.ts` that TypeScript ships with, and `skipLibCheck` can skip checking all `d.ts` in a compilation.

> Using Faster Variance Checks

Why:

Without `--strictFunctionType`, type parameter is check by structure comparison of type, member by member. Read more about variance in [tsconfig](https://www.typescriptlang.org/tsconfig#strictFunctionTypes) and [handbook](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html).

### Configuring Other Build Tools

TypeScript may working with other build tools, please make sure you read up about performance in your choice.

> Concurrent Type-Checking

Why:

Serveral tools like `fork-ts-checker-webpack-plugin` or `awesome-typescript-loader` provides running type-checking in a saparate process which can reduce waiting time.

> Isolated File Emit

Why:

Features like `const enum` and `namespace` needing to check other files inorder to generate output which make emit slower even more conflict with tool like Babel. `isolatedModules` can tell us which feature might not be supported.

### Investigating Issues

> Disabling Editor Plugins

Why:

Editor experiences can be impacted by plugins. If you have any troubles, try disabling plugins or reading their guides.

> `extendedDiagnotics`

Why:

If the compile time over your expecting, try run TypeScript with `--extendedDiagnotics` to print out the compiler spending time info. For the detail about each fields, read [this](https://github.com/microsoft/TypeScript/wiki/Performance#extendeddiagnostics).

> `showConfig`

Why:

`tsconfig.json` can extends other configuration files, running with `showConfig` will print the final calculate version.

> `traceResolution`

Why:

With `traceResolution`, the reason of each file was included in a compilation is emit verbosely. If any files than shouldn't include, please review the `include` / `exclude` settings or even more like `types`, `typeRoots` or `paths`.

> Running `tsc` Alone

Why:

Using 3rd party build tools with TypeScript may run into slow performance. Make sure you compare the diagnotics information and configuration between `tsc` and the build tool.

> Upgrading Dependencies

Why:

Sometimes TypeScript's type-checking can be impacted by computationally intensive `.d.ts` files. This is rare, but can happen. Upgrading to a newer version of TypeScript (which can be more efficient) or to a newer version of an `@types` package (which may have reverted a regression) can often solve the issue.

### Common Issues

> Misconfigured `include` and `exclude`

Suggestion config for `include` and `exclude`

```json
{
  // include only source files
  "include": ["src"],
  // prevent deeper node_modules and dot files
  "exclude": ["**/node_modules", "**/.*/"]
}
```

### Filing an Issue

If your project is already properly and optimally configured, you may want to [file an issue](https://github.com/microsoft/TypeScript/issues/new/choose).

A few steps you can follow before file an issue:

- Make sure you're not hitting a resolved issue by testing your problem with the nightly version.
- Profiling the compiler by several flag like `--trace-ic`, `generateCpuProfile`.
- If the issue is about editing performance, please collecting a TSServer log in Visual Studio Code.

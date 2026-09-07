## 1. Project skeleton

- [ ] 1.1 Add `package.json` (name `hello-cli`, `"type": "module"`, no dependencies, `bin.hello` → `bin/hello.js`, `scripts.test` → `node --test`) and verify `node -e "require('./package.json')"` parses it and `npm test` runs without a manifest error
- [ ] 1.2 Create the `bin/`, `src/`, and `test/` directories with placeholder module files and verify `node bin/hello.js` runs without a module-resolution error

## 2. Core logic (`src/hello.js`)

- [ ] 2.1 Implement `greet(name)` returning `Hello, <name>!` verbatim, and verify by unit test with `Ada`, `Ada Lovelace`, and `José`
- [ ] 2.2 Implement a `UsageError` class and `parseArgs(argv)` that returns either a help request or a validated name, throwing `UsageError` for zero arguments, an empty/whitespace-only argument, and two or more arguments — verify each case by unit test asserting the error type and message
- [ ] 2.3 Implement `usage()` returning the usage text naming the command and its `<name>` argument, and verify by unit test that the text mentions both

## 3. CLI entry point (`bin/hello.js`)

- [ ] 3.1 Add the `#!/usr/bin/env node` shebang, wire `process.argv.slice(2)` through `parseArgs`, print the greeting on stdout, and verify `node bin/hello.js Ada` prints exactly `Hello, Ada!`
- [ ] 3.2 Handle `--help`/`-h` by printing `usage()` to stdout with exit code 0 even when other arguments are present, and verify `node bin/hello.js --help` and `node bin/hello.js --help Ada` both print usage and exit 0 (`echo $?`)
- [ ] 3.3 Catch `UsageError`, write the message plus usage to stderr, and set `process.exitCode = 1` (never call `process.exit()`, per design.md); verify `node bin/hello.js` prints no stdout, prints a usage message on stderr, and exits non-zero

## 4. Tests

- [ ] 4.1 Write `test/hello.test.js` unit tests covering every scenario in `specs/hello-cli/spec.md` for `greet`, `parseArgs`, and `usage`, and verify `npm test` passes with all scenarios represented
- [ ] 4.2 Add end-to-end tests that spawn `bin/hello.js` as a subprocess to assert stdout/stderr routing and exit codes for the success, help, no-argument, empty-name, and too-many-arguments cases, and verify `npm test` passes
- [ ] 4.3 Verify no stack trace reaches stderr on any invalid invocation by asserting stderr does not contain `at ` frames or the string `Error:` prefixed by a throw site

## 5. Wrap-up

- [ ] 5.1 Update `README.md` with a short usage section (`node bin/hello.js <name>`) and verify the documented commands run as written
- [ ] 5.2 Run `npm test` and `openspec validate hello-cli --strict` and verify both pass with no errors

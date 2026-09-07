## Why

This repository is a practice ground for the agentic SDLC workflow and currently contains no executable code — only OpenSpec scaffolding and a README. A small, fully specified CLI gives the propose → apply → archive loop something real to operate on, and gives the repo its first working command with an established layout for later changes.

## What Changes

- Add a `hello` CLI entry point that prints `Hello, <name>!` to stdout when given a name as a command-line argument.
- Accept the name as the first positional argument (`hello Ada` → `Hello, Ada!`).
- Add basic error handling: a missing name, an empty/whitespace-only name, or extra unexpected arguments produce a usage message on stderr and a non-zero exit code, not a stack trace.
- Add a `--help`/`-h` flag that prints usage to stdout and exits 0.
- Add a Node.js project skeleton (`package.json`, `bin/` entry point, `src/` module) plus unit tests covering the success and error paths.

No existing behavior is modified or removed; nothing here is breaking.

## Capabilities

### New Capabilities
- `hello-cli`: Greeting a named person from the command line, including argument parsing, usage/help output, and error handling with exit codes.

### Modified Capabilities

None — this is the first capability in the repository.

## Impact

- **New code**: `package.json`, `bin/hello.js` (executable entry point), `src/hello.js` (pure greeting/validation logic), `test/hello.test.js`.
- **Runtime**: Node.js (already available in this environment; the repo's `.gitignore` is already set up for Node). Assumption recorded here for review: JavaScript on Node was chosen over Python or a compiled language because it needs no build step and no third-party dependencies.
- **Dependencies**: none — argument parsing is done by hand against `process.argv`, and tests use Node's built-in `node:test` runner.
- **APIs / systems**: none. No network, no filesystem writes, no configuration.

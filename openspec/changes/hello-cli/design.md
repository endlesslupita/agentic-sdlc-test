## Context

The repository is greenfield: no source tree, no package manifest, no test runner. See `proposal.md` — Why for motivation, and `specs/hello-cli/spec.md` for the behavior contract. Because nothing exists yet, this design's main job is to pin the two choices that would otherwise be made implicitly during implementation — runtime and project layout — and to keep both as small as the spec allows.

## Goals / Non-Goals

**Goals:**
- Establish a minimal, conventional project layout that later changes can extend.
- Keep the greeting and validation logic separate from process concerns (`process.argv`, `console`, exit codes) so it is directly unit-testable.
- Zero third-party dependencies.

**Non-Goals:**
- A general argument-parsing framework, subcommands, or configuration files.
- Packaging or publishing to a registry; internationalization; input sanitization beyond the emptiness check in the spec.

## Decisions

**Runtime: Node.js (JavaScript), no build step.** Node 22 is already present in this environment, the repo's `.gitignore` is already written for a Node project, and OpenSpec itself is a Node tool — so the toolchain is already in place. Alternatives considered: Python (equally available, but adds a second ecosystem to a repo already leaning Node) and C (matches low-level familiarity but drags in a compiler, build files, and a test harness for what is a three-line program). This is the most reversible decision here — the spec is written in terms of stdout/stderr/exit codes, so a rewrite in another language would leave the spec untouched.

**Hand-rolled argument parsing over a library.** The spec's surface is one positional argument plus `--help`/`-h`. Reading `process.argv.slice(2)` directly is a handful of lines and avoids a dependency. Alternatives considered: Node's built-in `util.parseArgs` (reasonable, but geared toward flags and adds indirection for a single positional) and `commander`/`yargs` (both are dependencies that dwarf the program).

**Split pure logic from the process shell.** `src/hello.js` exports pure functions — build the greeting, validate arguments, produce the usage text — that return values or throw a typed error. `bin/hello.js` is the only file that touches `process.argv`, `console`, and `process.exit`. This keeps every scenario in the spec testable without spawning a subprocess, though the end-to-end exit-code scenarios are still worth one subprocess test each.

**Errors as a thrown typed error, caught at the boundary.** Validation throws a `UsageError`; `bin/hello.js` catches it, writes `error.message` plus usage to stderr, and exits non-zero. This is what keeps the spec's "no stack trace on invalid input" scenario true, and it means an *unexpected* error still surfaces loudly rather than being swallowed. Exit code 1 for all usage errors — the spec only requires non-zero, so a single code keeps it simple.

**Tests use the built-in `node:test` runner.** Ships with Node, needs no dependency or config, and `npm test` wires to it in one line. Alternatives considered: Jest and Vitest, both of which are dependencies plus config for a test suite of roughly a dozen assertions.

## Risks / Trade-offs

- **Hand-rolled parsing grows awkward if the CLI gains flags later** → Acceptable now; the pure-function split means swapping in `util.parseArgs` later touches one file and no spec.
- **Node's stdout writes are asynchronous when piped, so `process.exit()` can truncate output** → Set `process.exitCode` and let the process end naturally instead of calling `process.exit()`.
- **Choosing JavaScript for a repo whose owner is newer to JS** → Mitigated by keeping the code dependency-free, unbuilt, and small enough to read in one sitting; flagged in the proposal for review before implementation, and cheap to redo in another language since the spec is language-neutral.
- **Argument values that look like flags (e.g. a name of `--help`) are ambiguous** → The spec makes `--help` win; documented behavior rather than a bug.

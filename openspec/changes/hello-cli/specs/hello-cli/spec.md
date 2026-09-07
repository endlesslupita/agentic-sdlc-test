## Purpose

Greets a named person from the command line, so the repository has one small, fully specified executable command that exercises argument handling, usage output, and error reporting end to end.

## ADDED Requirements

### Requirement: Greeting a named person

The CLI SHALL accept a name as its first positional argument and SHALL write `Hello, <name>!` followed by a newline to standard output, exiting with status code 0.

The name SHALL be used verbatim, without trimming, case changes, or other transformation, except that leading and trailing whitespace is trimmed only for the purpose of the emptiness check in the error requirement below.

#### Scenario: Name supplied

- **WHEN** the CLI is invoked as `hello Ada`
- **THEN** standard output contains exactly `Hello, Ada!` followed by a newline
- **AND** standard error is empty
- **AND** the exit code is 0

#### Scenario: Name containing spaces

- **WHEN** the CLI is invoked with a single argument whose value is `Ada Lovelace`
- **THEN** standard output contains exactly `Hello, Ada Lovelace!` followed by a newline
- **AND** the exit code is 0

#### Scenario: Name containing non-ASCII characters

- **WHEN** the CLI is invoked with a single argument whose value is `José`
- **THEN** standard output contains exactly `Hello, José!` followed by a newline
- **AND** the exit code is 0

### Requirement: Reporting invalid usage

The CLI SHALL reject invalid invocations with a usage message on standard error and a non-zero exit code, and SHALL NOT print a greeting or an uncaught runtime error (such as a stack trace) in these cases.

An invocation is invalid when: no positional argument is given; the positional argument is empty or consists only of whitespace; or more than one positional argument is given.

#### Scenario: No name given

- **WHEN** the CLI is invoked with no arguments
- **THEN** standard error contains a usage message naming the command and its expected argument
- **AND** standard output is empty
- **AND** the exit code is non-zero

#### Scenario: Empty or whitespace-only name

- **WHEN** the CLI is invoked with a single argument that is empty or contains only whitespace
- **THEN** standard error contains a message stating that the name must not be empty
- **AND** standard output is empty
- **AND** the exit code is non-zero

#### Scenario: Too many arguments

- **WHEN** the CLI is invoked with two or more positional arguments
- **THEN** standard error contains a message stating that exactly one name is expected
- **AND** standard output is empty
- **AND** the exit code is non-zero

#### Scenario: No stack trace on invalid input

- **WHEN** the CLI is invoked with any invalid combination of arguments
- **THEN** standard error contains a human-readable usage or error message
- **AND** standard error does not contain a language runtime stack trace

### Requirement: Requesting help

The CLI SHALL support `--help` and `-h`, which SHALL print usage information to standard output and exit with status code 0, regardless of any other arguments present.

#### Scenario: Help flag requested

- **WHEN** the CLI is invoked with `--help` or `-h`
- **THEN** standard output contains usage information describing the command and its name argument
- **AND** the exit code is 0

#### Scenario: Help flag takes precedence

- **WHEN** the CLI is invoked with `--help` alongside other arguments
- **THEN** usage information is printed to standard output and no greeting is printed
- **AND** the exit code is 0

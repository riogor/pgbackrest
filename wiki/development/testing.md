# Test runner and harness architecture

[Wiki home](../README.md)

The framework combines Perl orchestration, C build tooling, generated per-suite executables, and reusable C harnesses.

```text
test/test.pl + test/define.yaml
             ↓
   C test builder + test/src/test.c wrapper
             ↓
  production modules + harnesses + selected *Test.c
             ↓
   assertions / logs / coverage / optional Valgrind
```

## Suite definition

`test/define.yaml` groups suites into modules and declares top-level run counts, coverage modules, dependencies, harnesses/shims, and binary/container requirements. `command/archive-get` has five runs, requires the pgBackRest binary, and covers archive-common/get code.

The generated test includes relevant production `.c` files and the suite in one translation unit. Tests can call static internal functions. Harness templates can replace selected functions while retaining original implementations where supported.

## Shared harnesses

| Harness | Responsibility |
|---|---|
| `harnessTest`, `harnessLog` | Run selection, typed assertions, exact logs, diagnostics |
| `harnessConfig` | Real parsing with controlled test options |
| `harnessStorage`, `harnessInfo` | Files/paths and checksummed/encrypted metadata |
| `harnessPostgres`, `harnessPq` | Synthetic physical data and scripted SQL |
| `harnessProtocol`, `harnessFork` | Real handlers in controlled workers and synchronization |

Between selected top-level runs, `testBegin()` checks leftover logs and clears test/data directories. `TEST_TITLE()` and loop iterations do not create isolation.

`--vm=none` runs locally. VM names select container environments. Functional success, coverage, and Valgrind are distinct checks: a test can pass its assertions yet fail on memory access.

See [writing tests](writing-tests.md), [archive-get example](archive-get-example.md), and [CI](ci.md).

## Source entry points

- [test/test.pl](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/test.pl)
- [test/define.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/define.yaml)
- [test/src/test.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/test.c)
- [test/src/command/test/build.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/command/test/build.c)
- [test/lib/pgBackRestTest/Common/JobTest.pm](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/lib/pgBackRestTest/Common/JobTest.pm)

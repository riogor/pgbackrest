# Coverage declarations and memory checks

[Wiki home](../README.md)

Scope: checks beyond normal C assertions. [Test overview](../development/testing.md).

`test/define.yaml` declares covered/dependent modules and suite properties. Generated builds instrument the appropriate C code; coverage tooling reads compiler reports and maps results back to functions/lines/branches.

Coverage annotations in code distinguish deliberately untestable paths from missing tests. Use the project's recognized forms and explain why a path is unreachable or impractical. An annotation is a maintenance assertion, not a general escape hatch for inconvenient branches.

## Separate checks

Functional assertions establish expected results for selected inputs. Coverage reveals which paths were exercised. Valgrind checks memory behavior such as invalid reads or leaks. Formatter/generation checks enforce other contracts. Passing one does not imply the others pass.

ADBM-3528's GOST test passed normally but Valgrind found a borrowed driver dereferenced after its filter was freed. Disabling Valgrind is useful to isolate functional failures, not a fix for that bug.

## Environment effects

Compiler/gcov versions, supported platform instructions, and debug symbols affect tooling. Separate a tool startup/report parsing failure from an actual test-code memory violation. Record disabled checks when reporting local results.

Use the smallest suite/run during development, then restore the relevant instrumentation. See [CI](../development/ci.md), [memory ownership](../runtime/memory-contexts.md), and [archive-get example](../development/archive-get-example.md).

## Source entry points

- [test/define.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/define.yaml)
- [test/src/command/test/coverage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/command/test/coverage.c)
- [test/src/module/test/coverageTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/test/coverageTest.c)
- [test/lib/pgBackRestTest/Common/JobTest.pm](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/lib/pgBackRestTest/Common/JobTest.pm)

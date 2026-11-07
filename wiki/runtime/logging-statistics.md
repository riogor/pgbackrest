# Logging, tracing, and statistics

[Wiki home](../README.md)

Scope: observing execution without changing its behavior. [Core overview](../architecture/core-runtime.md).

`common/log.c` routes configured log levels to console/file outputs with process identifiers and formatting. `command/command.c` emits command lifecycle information and formats resolved options, redacting those marked secure.

Function tracing uses typed `FUNCTION_LOG_*` parameter/return macros. `FUNCTION_TEST_*` and harness variants have different roles in instrumentation. Do not replace a secret's deliberately non-logging parameter macro with an ordinary log macro for convenience.

## Counters

`statInit()` initializes a lazily populated statistics collection. `statInc()` increments a string-keyed counter; `statToJson()` emits it. The implementation uses a sorted list, so counters are intended for significant/high-latency events rather than every hot-loop iteration.

HTTP/TLS code counts requests, retries, and sessions. These have different meanings: many requests can reuse a session, while connection retries can occur before an HTTP request succeeds. Interpret counters at the layer that owns them.

## Tests

`TEST_RESULT_LOG()` compares and consumes captured output. Unexpected warnings are failures, even if the tested function returned success. Log replacement facilities normalize selected unstable values; use them deliberately rather than hiding meaningful differences.

The archive-get GOST test needed explicit success-log assertions. OpenSSL alias normalization also produced legitimate warnings that required version-aware fixtures.

See [coverage](../testing/coverage.md), [archive-get example](../development/archive-get-example.md), and [HTTP](../network/http.md).

## Source entry points

- [src/common/log.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/log.c)
- [src/common/stat.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/stat.h)
- [src/command/command.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/command.c)
- [test/src/common/harnessLog.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessLog.c)

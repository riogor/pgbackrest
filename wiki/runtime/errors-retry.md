# Errors, retry history, and timed waits

[Wiki home](../README.md)

Scope: reporting failures across repeated attempts. [Core overview](../architecture/core-runtime.md).

Typed errors carry a code/type, message, and stack context. `THROW*` creates a failure, `RETHROW()` preserves one, and nested `TRY`/`CATCH` blocks may replace or augment it. If a diagnostic probe itself can throw, preserve the original error before probing: `infoLoadErrorRethrow()` demonstrates this pattern.

## Waiting is separate from reporting

`Wait` implements a time budget with Fibonacci backoff. `waitMore()` waits and decides whether another attempt is allowed; `waitRemains()` exposes remaining time. The contract allows initial retry opportunities even when an operation has consumed the initial budget, so do not infer exact attempt count from timeout alone.

`ErrorRetry` collects failures across attempts. It retains the initial message and summarizes repeated later error messages with timing/count information. It does not itself schedule retries or decide whether an operation is safe to repeat.

HTTP requests use both pieces: wait/backoff controls retries, while accumulated errors explain why the operation ultimately failed. Other callers use timeout mechanisms without the same aggregation.

## When changing a retry loop

Preserve the final typed error and useful first failure. Check which request state must be recreated after a failure, and which partial effects may already exist. Avoid turning a configuration/format error into an unexplained timeout. Test differing errors across attempts, not just repeated identical errors.

See [HTTP requests](../network/http.md), [metadata fallback](../metadata/info-loading.md), and [CI diagnosis](../development/ci.md).

## Source entry points

- [src/common/error/error.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/error/error.h)
- [src/common/error/retry.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/error/retry.c)
- [src/common/wait.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/wait.h)
- [src/common/io/http/request.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/http/request.c)
- [src/info/info.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/info.c)

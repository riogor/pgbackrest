# Memory, objects, errors, and logging

[Wiki home](../README.md)

Shared C conventions underpin every subsystem. Ownership is managed through contexts and objects rather than independent malloc/free calls throughout command code.

## Ownership

Allocations belong to a `MemContext`. Freeing a context frees its allocations and child contexts. Objects generally own a context; `objFree()` releases that tree. Constructors use `OBJ_NEW_BEGIN/END`, and temporary work commonly uses `MEM_CONTEXT_TEMP_BEGIN/END`.

A value allocated inside a temporary context must be moved to a surviving context before returning it. `*Move()` functions and `memContextPrior()` often mark this transfer. Resource callbacks free non-memory resources such as OpenSSL handles.

Extracted driver pointers are borrowed. In the GOST test failure, `ioFilterFree(blockDecryptFilter)` freed the driver later accessed as `blockDecrypt->cipher`. A normal run could pass because stale bytes were still readable; Valgrind detected the lifetime violation.

## Errors and diagnostics

`TRY_BEGIN`, `CATCH`, `CATCH_ANY`, `FINALLY`, and `THROW*` implement exception-like control flow integrated with context cleanup and stack traces. `ASSERT` expresses internal invariants; errors such as `CryptoError` and `FileMissingError` represent operational failures.

`FUNCTION_LOG_*`, `FUNCTION_TEST_*`, and `FUNCTION_HARNESS_*` provide function/parameter tracing conventions. Secret parameters must use the appropriate non-logging convention. Command logging also redacts secure options.

Shared types include `String`, `Buffer`, lists, variants, JSON, and `Pack`. `StringId` is compact and length-constrained, not a general string. Long cipher names require explicit mappings; this branch validates exact strings to avoid accepting compact-ID collisions.

See [change guide](../development/change-guide.md) and the source coding standards for consistent ownership/naming practices.

## Focused guides

- [Memory contexts](../runtime/memory-contexts.md)
- [Errors and retries](../runtime/errors-retry.md)
- [Locks and stop markers](../runtime/locks-stop-state.md)
- [Logging/statistics](../runtime/logging-statistics.md)
- [Buffers/strings/identifiers](../runtime/buffers-strings.md)
- [Pack encoding](../runtime/pack.md)


## Source entry points

- [src/common/memContext.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/memContext.h)
- [src/common/type/object.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/object.c)
- [src/common/error/error.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/error/error.h)
- [src/common/debug.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/debug.h)
- [src/common/type/stringId.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/stringId.c)

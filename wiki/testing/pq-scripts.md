# Scripted libpq tests

[Wiki home](../README.md)

Scope: deterministic SQL client/database behavior without a running server. [Database overview](../architecture/database.md).

`harnessPq` scripts libpq calls and results. Entries identify the logical session, function, parameters, and return data. Supported calls include connection/status, send/consume/result processing, field/row access, cancellation, and cleanup.

Macros such as `HRN_PQ_SCRIPT_OPEN` expand common sequences; query-specific helpers reduce repeated boilerplate. Result type OIDs matter because the real client converts returned PostgreSQL types into project values.

## Strict expectations

A scripted call sequence checks what the production client requests, not just its final result. A new setup query or changed cancellation path can require updating scripts even when command output is unchanged. That is useful when intentional, but avoid disguising an unexpected extra query by indiscriminately relaxing expectations.

Multiple logical sessions can represent primary/standby or reconnect behavior. Keep each connection's sequence coherent; do not mix physical file metadata for one version/identity with scripted SQL for another unless testing that mismatch.

## Test coverage

Cover connection failure, unsupported result types, nulls, timeout/cancel behavior, and state transitions. These tests do not validate live server SQL syntax/semantics for every version; actual integration tests still matter.

See the PostgreSQL client suite first for concrete script patterns, then [standby backup](../backup/standby.md) for a command-level consumer.

## Source entry points

- [test/src/common/harnessPq.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessPq.h)
- [test/src/common/harnessPq.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessPq.c)
- [test/src/module/postgres/clientTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/postgres/clientTest.c)
- [src/postgres/client.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/postgres/client.c)

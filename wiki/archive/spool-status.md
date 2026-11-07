# Spool acknowledgements and error files

[Wiki home](../README.md)

Scope: coordination between database-facing archive calls and background processing. [WAL workflow](../workflows/wal-archive.md).

`archiveAsyncStatus()` checks a requested file's `.ok`; absent that, it checks the file's `.error`, then a global error. Nonempty status files encode a numeric code and message. Empty successful status and warning-carrying success are different cases.

The get and push modes map onto different spool queues. Async get also stores prefetched WAL there, while push coordination primarily acknowledges processing of source WAL. The spool is not a substitute for the repository.

## Races and stale state

`archiveAsyncErrorClear()` removes old errors before forking background work, avoiding an immediate stale failure while a new attempt is pending. A test should establish process order with harness synchronization rather than an arbitrary delay.

Restore removes a stanza's archive spool state so acknowledgements from an older cluster cannot cause incorrect decisions for the restored one. This is an intentional consistency boundary.

## Interpreting status

Whether an error should throw or a warning should be logged depends on the caller's flags and mode. In get flows, an empty `.ok` may acknowledge a missing segment rather than a fetched data file. Inspect the caller before interpreting status as evidence of bytes available.

Tests should check status content, removal/consumption, global versus per-file precedence, and the actual destination/archive state.

See [async push](async-push.md), [async get](async-get.md), and [locks](../runtime/locks-stop-state.md).

## Source entry points

- [src/command/archive/common.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/common.c)
- [src/command/archive/common.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/common.h)
- [src/command/restore/restore.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/restore.c)
- [test/src/module/command/archiveGetTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/archiveGetTest.c)

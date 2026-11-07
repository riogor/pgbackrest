# Asynchronous WAL get and queue planning

[Wiki home](../README.md)

Scope: background prefetch for database recovery. [Push/get overview](../workflows/wal-archive.md).

The main `cmdArchiveGet()` path checks available spool state and triggers/waits for background work when needed. `cmdArchiveGetAsync()` finds requested archive candidates, dispatches workers, and publishes fetched WAL/status for the main process.

`queueNeed()` decides which segment names are needed given the requested position, queue contents, capacity, segment size, and PostgreSQL version. It accounts for files/status markers rather than assuming directory entry count equals queued payload size.

## Consumption and missing WAL

Prefetched files move through spool/destination operations. Success status can carry warnings; a missing-segment acknowledgement is distinct from a decode or destination-write error. `archive-missing-retry` changes how missing results are retried/consumed.

A queue with future segments does not prove the currently requested one exists. Likewise, a found encrypted archive file does not prove the destination path can be opened. The GOST test initially failed on the missing `pg_wal` directory, not decryption.

## Tests

Queue tests can call the static helper directly. Async command tests install local handler shims and inspect fetched files and status. Lock/timeout cases use explicit forks. Keep segment size/version consistent in fixtures and cover queue refill, junk entries, and missing-file behavior.

See [archive-get walkthrough](../development/archive-get-example.md), [spool status](spool-status.md), and [WAL names](wal-identity.md).

## Source entry points

- [src/command/archive/get/get.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/get.c)
- [src/command/archive/get/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/file.c)
- [test/src/module/command/archiveGetTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/archiveGetTest.c)

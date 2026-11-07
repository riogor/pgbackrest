# Asynchronous WAL push

[Wiki home](../README.md)

Scope: how background push finds and processes source WAL. [Push/get overview](../workflows/wal-archive.md).

`cmdArchivePush()` is the database-facing path; `cmdArchivePushAsync()` performs queued transfer. Helpers collect ready WAL from the database archive-status area and decide what still needs processing based on spool acknowledgements.

The async coordinator validates configuration/identity, builds work, dispatches file jobs, and writes success/error status. File workers handle actual repository transfer; acknowledgements are only meaningful relative to the configured repository and source identity checks.

## Multiple repositories and repeats

Per-file work can involve multiple repositories. A file already present with the same checksum is a recognized repeat; a conflicting archived payload is a failure. Preserve error attribution to repository and segment when combining results.

Queue-limit/drop handling is an explicit path with warnings/status effects. It is not equivalent to successful archival of the bytes. Read `archivePushDrop()` before changing queue sizing or interpreting its acknowledgements.

## Test boundaries

Create valid `pg_control`, archive metadata, source WAL, and ready markers for independent tests. The new GOST push test originally lacked control data because a previous top-level run's fixtures had been cleared.

Assert both expected logs and actual repository/status files. Exercise duplicate transfers and partial multi-repository errors, not only a single clean upload.

See [spool status](spool-status.md), [WAL identity](wal-identity.md), and [protocol shims](../testing/protocol-shims.md).

## Source entry points

- [src/command/archive/push/push.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/push/push.c)
- [src/command/archive/push/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/push/file.c)
- [src/command/archive/push/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/push/protocol.c)
- [test/src/module/command/archivePushTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/archivePushTest.c)

# WAL archiving: push and get

[Wiki home](../README.md)

`archive-push` sends database WAL to repositories. `archive-get` supplies archived WAL to recovery. Both expose a database-facing command and asynchronous processing paths.

## Push

PostgreSQL calls archive-push through `archive_command`. The command checks database/repository identity, loads archive metadata, and streams WAL through compression/encryption. Names incorporate content hashes. Repeated identical WAL is recognized; conflicting content for the same WAL identity is an error.

Async push processes ready WAL in parallel and uses spool status acknowledgements/errors. The invoking process can consult status rather than do every transfer itself. The push spool is not the durable repository of WAL; source WAL and archive destinations remain central.

## Get

Recovery supplies a requested WAL/history filename and destination. `archiveGetCheck()` loads relevant metadata; `archiveGetFind()` resolves candidates; `archiveGetFile()` reads, decrypts/decompresses, and writes content. A missing segment and a failed decode/write are different outcomes.

Async get prefetches into the spool. `queueNeed()` computes requested segments from the current segment, version, segment size, and queue capacity. `.ok`/`.error` files communicate results. A zero-length `.ok` can represent a missing segment; interpretation depends on caller state.

## Debugging boundaries

Validate stanza/archive identity before debugging codecs. Check destination directories separately from repository paths. When the main process times out, inspect spool status and the async log. Stale spool acknowledgements can affect a restored cluster; restore clears the stanza archive spool for this reason.

GPDB filtered recovery can additionally transform WAL through version-specific processors. See [partial restore](../fork/partial-restore.md) and the [archive-get test walkthrough](../development/archive-get-example.md).

## Source entry points

- [src/command/archive/push/push.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/push/push.c)
- [src/command/archive/push/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/push/file.c)
- [src/command/archive/get/get.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/get.c)
- [src/command/archive/get/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/file.c)

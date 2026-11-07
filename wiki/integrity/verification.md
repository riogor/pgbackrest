# Verify planning and per-file checks

[Wiki home](../README.md)

Scope: repository validation beyond a live archiving check. [Management overview](../workflows/repository-management.md).

`verifyProcess()` loads archive/backup info, checks history, builds lists/ranges of WAL and backup data, and dispatches jobs. Results are collected into repository/archive/backup reporting structures; missing and invalid items remain distinguishable.

## File worker

`verifyFile()` reads a specified offset/limit, adds decryption and decompression when requested, then SHA-1 and size filters. A sink discards processed bytes so they need not be returned over a remote connection. It compares results and returns missing/checksum-mismatch/size-invalid or success as appropriate; codec/read failures can throw separately.

The coordinator may validate stored bytes directly or decoded logical content, depending on manifest data and job setup. Parameters must describe the same representation as the expected hash and size.

## Metadata and archive relationships

Info/manifests have their own load/checksum/identity checks before file work. Archive range analysis relates backups to required WAL history. A good payload checksum cannot repair wrong metadata or missing dependencies.

Test both planning/reporting and workers. Include missing files, corruption, incorrect sizes, encrypted/compressed data, and history mismatches. `check` tests live configuration/archiving behavior and is not a synonym for verify.

See [Pack](../runtime/pack.md), [remote storage](../storage/remote.md), [checksum purposes](checksums.md), and [metadata loading](../metadata/info-loading.md).

## Source entry points

- [src/command/verify/verify.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/verify.c)
- [src/command/verify/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/file.c)
- [src/command/verify/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/protocol.c)
- [test/src/module/command/verifyTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/verifyTest.c)

# Restore and database recovery

[Wiki home](../README.md)

`cmdRestore()` materializes a selected backup. Database startup then performs recovery using the written configuration and archived WAL. These phases have separate responsibilities.

## Main sequence

Restore checks PostgreSQL locality and validates the target path, clears old stanza archive spool state, selects a backup/repository, and loads the manifest. It validates/maps tablespaces, links, ownership, and paths for the destination.

Target preparation is followed by parallel file work. `restoreFile()` fetches referenced files or bundle regions, applies decryption/decompression, reconstructs block deltas, and validates expected content. Restore writes version-appropriate recovery settings/signals and WAL retrieval options.

Useful starting points are `restoreBackupSet()` for selection, `restoreManifestMap()` for mappings, `restoreCleanBuild()` for filesystem preparation, and `restoreJobCallback()`/`restoreFileProtocol()` for worker parameters.

## Modes and dependencies

Delta restore can reuse matching target data, but the selected manifest still defines the result. An incremental backup may depend on files/blocks from older backups, so its directory alone is not necessarily a self-contained restore source.

Recovery targets such as a time or named restore point become database recovery configuration. The database controls replay progress and target completion; pgBackRest supplies files and WAL.

The fork's `filter` option is validated for supported GPDB versions and affects both relation-file selection and WAL processing. It is distinct from ordinary delta restore or database selection.

Actual restore can remove/replace target data. This page explains source flow, not a universal execution procedure. See [manifests](../formats/manifests.md), [block reconstruction](../formats/block-incremental.md), [encryption](../formats/encryption.md), and [partial restore](../fork/partial-restore.md).

## Source entry points

- [src/command/restore/restore.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/restore.c)
- [src/command/restore/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/file.c)
- [src/command/restore/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/protocol.c)
- [src/command/restore/blockDelta.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/blockDelta.c)

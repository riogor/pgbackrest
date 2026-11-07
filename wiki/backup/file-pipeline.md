# Backup file workers and filter results

[Wiki home](../README.md)

Scope: one file's path through a backup worker. [Backup overview](../workflows/backup.md).

The coordinator's manifest/queue logic chooses work. `backupFile()` handles file processing and returns the measurements/decisions needed to update manifest entries. The protocol handler translates packed parameters into that API.

## Data stages

Source bytes are read through hash/size filters and, when enabled for suitable relations, page-checksum validation. Compression and encryption transform stored content. For block incremental mode, prior maps and block/super-block processing replace a simple whole-file encoding.

A second hash/size measurement can describe repository bytes after transformations. Consequently a logical file checksum/size and repository checksum/size are not interchangeable. Place a filter on the intended side of transformations.

Worker results communicate actual copy outcomes, sizes, hashes, page-checksum details, and block-map results. The coordinator merges them into the manifest and progress reporting. Files can change or disappear during an online backup, so inspect supported skip/recheck branches rather than assuming every planned file produces a normal copy.

## Bundles and boundaries

Bundle offsets refer to stored byte positions. Raw encryption and block-map framing must match restore readers. A file worker completing its output does not publish the complete backup: backup stop/WAL checks/manifest finalization still remain.

Test normal, missing/changing, compressed/encrypted, and block paths at their actual result boundary. See [checksums](../integrity/checksums.md), [block representation](../formats/block-incremental.md), and [Pack](../runtime/pack.md).

## Source entry points

- [src/command/backup/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/file.c)
- [src/command/backup/file.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/file.h)
- [src/command/backup/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/protocol.c)
- [src/command/backup/backup.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/backup.c)

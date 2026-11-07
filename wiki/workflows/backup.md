# Backup workflow

[Wiki home](../README.md)

`cmdBackup()` coordinates database state, file transfer, WAL availability, and durable metadata publication. Workers perform streaming file work; the coordinator owns the plan.

## Main sequence

1. Validate execution locality/stop state and load or reconstruct `backup.info`.
2. Initialize database/storage access and find a prior manifest for differential/incremental work.
3. Start the database backup and collect start WAL/LSN information.
4. Build a manifest of files, paths, links, targets, and options.
5. Compare prior content, select references, and resume compatible interrupted work when possible.
6. Save in-progress state and process file jobs in parallel.
7. Stop the database backup and complete its metadata.
8. With archive checking enabled, check/wait for WAL needed for consistency; optionally copy archive data into the backup.
9. Finalize the manifest and update completed backup information. Main dispatch may run automatic expiration afterward.

Read `backupStart()`, `backupProcess()`, `backupStop()`, `backupArchiveCheckCopy()`, and `backupComplete()` when a backup copied files but did not reach completion.

## File work and representation

`command/backup/file.c` handles file processing. Filters calculate hashes/page-checksum results and apply compression/encryption. Worker results update manifest sizes, checksums, and stored locations. Bundling combines payloads; block incremental encoding stores changed portions plus maps.

Backup ancestry (full/differential/incremental) differs from representation (whole files versus block incremental). Resume eligibility also depends on manifest/settings compatibility, not just an existing backup directory.

See [manifests](../formats/manifests.md), [block incremental](../formats/block-incremental.md), and [WAL archiving](wal-archive.md). Primary command coverage is in `test/src/module/command/backupTest.c` and related file/block suites.

## Source entry points

- [src/command/backup/backup.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/backup.c)
- [src/command/backup/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/file.c)
- [src/command/backup/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/protocol.c)
- [test/src/module/command/backupTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/backupTest.c)

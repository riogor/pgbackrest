# Backup ancestry and interrupted-backup resume

[Wiki home](../README.md)

Scope: references versus reusing interrupted work. [Backup overview](../workflows/backup.md).

Differential/incremental backups compare against an appropriate prior manifest and can reference data already in retained backups. `backupBuildIncrPrior()` and `backupBuildIncr()` own this planning. Referenced files/blocks remain dependencies of the resulting backup.

Resume is different: it attempts to reuse files copied during an unfinished backup. It is not an arbitrary restart of every old backup directory.

## Resume eligibility on this baseline

`backupResumeFind()` considers only the latest backup candidate. A completed `backup.manifest` means it is not a resumable interrupted backup. A resumable candidate needs its manifest copy, `resume` enabled, and a new backup of type full.

The saved state must be readable and compatible. Checks include pgBackRest version, backup type, prior label, and compression/settings relevant to reuse. A version mismatch can deliberately abandon resume rather than support every historical intermediate representation.

`backupResumeClean()` walks existing state, removes unusable files/paths, and marks reusable entries. It preserves recovery-critical in-progress state while cleaning. Existing bytes without usable checksum/manifest correspondence are not automatically trusted.

## Tests and diagnosis

Distinguish a backup falling back from incremental planning from a full backup refusing interrupted state. Inspect the specific logged reason and compare manifests/options. Avoid loosening an eligibility guard without understanding cleanup and later worker checks.

See [manifest lifecycle](../formats/manifests.md), [retention dependencies](../integrity/retention.md), and [file processing](file-pipeline.md).

## Source entry points

- [src/command/backup/backup.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/backup.c)
- [src/info/manifest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/manifest.c)
- [test/src/module/command/backupTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/backupTest.c)

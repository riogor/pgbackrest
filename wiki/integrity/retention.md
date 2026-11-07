# Expiration, backup dependencies, and WAL ranges

[Wiki home](../README.md)

Scope: safe removal planning. [Management overview](../workflows/repository-management.md).

`cmdExpire()` processes selected/configured repositories, loads backup information, and applies the requested retention/expiration path. Count-based full retention, time-based retention, differential retention, and explicit backup expiration are distinct branches.

## Dependency handling

A full backup can have differential/incremental descendants; incremental references may reach into prior backups. Expiration must remove/update a dependency-consistent set. Sorting labels and deleting old-looking directories is not equivalent to applying these rules.

Archive cleanup computes ranges based on database history and retained backups/options. WAL needed to make a retained backup consistent must remain available even when a broader archive-retention window is shortened.

Backup and archive info plus physical repository contents must remain coherent across removal. Reconstruct/load behavior and partially completed cleanup are relevant failure cases.

## Options and dry run

Dry-run behavior is enforced partly through storage helpers, but callers still need correct reporting and planning. Do not use unrestricted local writes as a shortcut around repository write controls.

Tests should build overlapping ancestry and WAL ranges, exercise multiple repositories, and assert both metadata and removed/preserved files. Cover exact boundary cases and explicit backup selection, not just a simple full-backup count.

See [ancestry/resume](../backup/ancestry-resume.md), [history](../metadata/database-history.md), and [storage](../architecture/storage.md).

## Source entry points

- [src/command/expire/expire.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/expire/expire.c)
- [src/info/infoBackup.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoBackup.c)
- [src/storage/helper.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/helper.h)
- [test/src/module/command/expireTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/expireTest.c)

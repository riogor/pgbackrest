# Recovery settings and signal files

[Wiki home](../README.md)

Scope: configuration written after restoring files. [Restore overview](../workflows/restore.md).

Restore turns command options into database recovery settings. `restoreRecoveryOption()` builds option data; recovery-writing routines choose the appropriate representation for the database version and requested behavior.

Older supported versions use `recovery.conf`; newer versions use the relevant automatic configuration and recovery/standby signal files. The correct mechanism is version-dependent, so a textual substitution in one writer cannot implement all versions.

## Retrieval and target

Recovery configuration includes a `restore_command` invoking archive-get with suitable stanza/repository options. Target time, named restore point, and other supported target options are translated for the server. Standby/recovery choices also affect which marker files should exist.

An offline backup with no explicit restore type can default to no recovery configuration in the path documented by `cmdRestore()`, avoiding inappropriate signal generation. Check option defaults and manifest online/offline state together.

## Testing boundary

Assert exact written contents and which files are present/absent for each supported version/mode. A successful restore worker does not establish that the server can interpret its recovery configuration. Conversely, generated settings do not establish that enough WAL is available to reach a target.

See [archive-get](../workflows/wal-archive.md), [database history](../metadata/database-history.md), and [Greenplum recovery](../fork/greenplum.md).

## Source entry points

- [src/command/restore/restore.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/restore.c)
- [src/postgres/interface.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/postgres/interface.h)
- [test/src/module/command/restoreTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/restoreTest.c)

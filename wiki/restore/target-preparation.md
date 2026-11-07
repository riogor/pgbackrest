# Restore target validation and cleanup

[Wiki home](../README.md)

Scope: the destination before file workers run. [Restore overview](../workflows/restore.md).

`restorePathValidate()` checks the target environment and existing database markers. The command then loads/maps the manifest and prepares paths, links, permissions, and owners through the clean/build routines.

## Nonempty directories

Without the relevant delta/force behavior, restore expects destinations to satisfy emptiness/nonexistence rules. `restoreCleanBuildRecurse()` validates existing objects and removes/prepares entries according to the selected mode and manifest.

Delta/force settings do not mean arbitrary content is a valid database. When expected database markers cannot establish that the target is a valid `$PGDATA`, code can disable those options and require normal empty-target handling. Preserve this validation when changing cleanup logic.

Tablespace and link mappings create multiple destination roots with their own existence/ownership checks. Do not apply only the main directory's rules and ignore a mapped target outside it.

## Stale state

Restore clears stanza archive spool state before proceeding so old acknowledgements do not affect the restored cluster. This is distinct from clearing repository backups or database WAL.

Test existing files, directory/link type mismatches, ownership/mode handling, and mapped targets. Inspect the plan rather than manually removing data to make a test pass.

See [recovery settings](recovery-settings.md), [spool status](../archive/spool-status.md), and [storage capabilities](../architecture/storage.md).

## Source entry points

- [src/command/restore/restore.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/restore.c)
- [src/info/manifest.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/manifest.h)
- [test/src/module/command/restoreTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/restoreTest.c)

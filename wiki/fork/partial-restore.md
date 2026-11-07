# Partial restore and WAL filtering

[Wiki home](../README.md)

The fork's `filter` option selects physical relations for GPDB partial recovery. Selection must affect both restored files and later WAL processing so replay does not blindly treat omitted relations as restored data.

## Input and lookup

`relationFilterInit()` reads JSON from an absolute local path and builds a sorted database/table list. An illustrative shape is:

```json
[{"dbOid": 16384, "tables": [
  {"tablespace": 1663, "relfilenode": 20000}
]}]
```

These are physical IDs, not SQL object names. `tablespace` defaults to the default tablespace. Relfilenode 0 entries for GPDB 7 non-leaf partitions are ignored. `isRelationNeeded()` preserves system relations and matches ordinary relations by database, tablespace, and relfilenode. Sample IDs above are explanatory, not a workspace selection.

## Consumers and constraints

Restore queue construction applies relation selection to files. For WAL segment requests, `archiveGetFile()` can add `walFilterNew()`. The filter dispatches to GPDB-version-specific record processors and handles WAL page/record boundaries with explicit buffer-size constraints.

Restore accepts this option only for `fork=GPDB` with physical PostgreSQL versions 9.4 or 12, corresponding to supported GPDB 6/7 paths. Supporting another version requires more than changing that guard: physical layouts and record processing must also work.

Tests include the partial-restore and GPDB WAL-filter suites, archive-get's `wal Filter` run, and the Arenadata partial-restore/timeline scripts. Read them before changing relation or WAL-selection semantics.

See [database interfaces](../architecture/database.md) and [WAL archiving](../workflows/wal-archive.md).

## Source entry points

- [src/common/partialRestore.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/partialRestore.c)
- [src/common/walFilter/walFilter.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/walFilter/walFilter.c)
- [src/command/restore/restore.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/restore.c)
- [src/command/archive/get/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/file.c)
- [arenadata/scripts/helpers/partial_restore_helper.py](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/arenadata/scripts/helpers/partial_restore_helper.py)

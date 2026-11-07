# Greenplum and Greengage support

[Wiki home](../README.md)

The Arenadata fork adds GPDB-specific backup/recovery behavior. Repository guides describe Greenplum 6 and 7. The prepared Greengage environment exercises compatible code paths, but a local utility instance is not equivalent to a distributed cluster test.

## Cluster model

GPDB has a coordinator and multiple segments. The documented procedure assigns stanzas to the coordinator and each primary segment, for example `seg-1`, `seg0`, and `seg1`. Each has its own data path and port; one repository can hold their separate namespaces.

Use `fork=GPDB` where required. SQL operations in the guide use `PGOPTIONS='-c gp_session_role=utility'`. Build/runtime integration also requires the intended Greenplum/Greengage `pg_config` and libpq.

A recovery plan coordinates backups/WAL across stanzas. The guide uses `gp_create_restore_point()`; GPDB 6 additionally requires `gp_pitr` for restore-point support. A successful individual segment backup does not establish a complete distributed recovery procedure.

## Source and integration tests

`src/build/config/config.yaml` defines fork/filter options. Physical-format handling is under `postgres/interface/`. `common/partialRestore.c` selects relations, and `common/walFilter/` implements GPDB 6/7 WAL processing. Restore checks the supported fork/version combination.

`arenadata/scripts/` tests PITR, partial restore, and timeline switching. `arenadata/run_docker.sh` builds/runs a test container using a supplied GPDB image. This integration route is distinct from generic unit tests and the main upstream-style CI matrix.

Read the full GPDB guide for operational steps. Continue with [partial restore](partial-restore.md), [database interfaces](../architecture/database.md), or [local environment](../environment/local.md).

## Source entry points

- [README_GPDB_en.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/README_GPDB_en.md)
- [arenadata/README.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/arenadata/README.md)
- [arenadata/run_docker.sh](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/arenadata/run_docker.sh)
- [arenadata/scripts/test_pitr_gpdb.sh](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/arenadata/scripts/test_pitr_gpdb.sh)

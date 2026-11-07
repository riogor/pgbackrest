# Database and PostgreSQL interfaces

[Wiki home](../README.md)

SQL sessions, command-level database operations, and physical-file interpretation are related but distinct responsibilities.

| Layer | Responsibility |
|---|---|
| `postgres/client.c` | libpq-backed connections and SQL queries |
| `db/db.c`, `db/helper.c` | Command-level operations and primary/standby selection |
| `postgres/interface.c` | Control data, WAL headers, version-dependent formats |

`Db` exposes version, data directory, standby state, archive settings, control information, and related facts. Backup uses it for database coordination. `db/protocol.c` allows database operations to be delegated through a remote service.

Physical control data supplies version/system identity independently of a SQL connection. Commands compare it against SQL/repository information to catch an incorrect stanza, path, port, or database incarnation. Correct SQL connectivity alone does not prove that a configured directory belongs to that server.

Version-specific interfaces are generated using `src/build/postgres/postgres.yaml` and its renderers. Vendor-derived structures live under `postgres/interface/`; a physical layout valid for one version should not be assumed valid for another.

Page checksums concern physical database pages. They differ from whole-file backup hashes and block change-detection hashes. Each validates a different property.

Tests synthesize control/WAL data with `harnessPostgres` and script query behavior with `harnessPq`. Many command/unit tests therefore do not need a running server; integration tests exercise actual processes and services.

Read [Greenplum](../fork/greenplum.md) and [partial restore](../fork/partial-restore.md) before changing fork/version-specific behavior.

## Source entry points

- [src/postgres/client.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/postgres/client.c)
- [src/db/db.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/db/db.h)
- [src/db/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/db/helper.c)
- [src/postgres/interface.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/postgres/interface.h)
- [src/build/postgres/postgres.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/postgres/postgres.yaml)

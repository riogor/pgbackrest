# Source map

[Wiki home](README.md)

Paths below are relative to the repository root. Read a public header for the interface, then its implementation and corresponding tests.

| Path | Responsibility | Detail |
|---|---|---|
| `src/main.c` | Initialization and command/role dispatch | [Runtime](architecture/runtime.md) |
| `src/command/` | User commands and file-worker handlers | [Backup](workflows/backup.md) |
| `src/config/` | Configuration parsing and typed access | [Configuration](architecture/configuration.md) |
| `src/info/` | Info files, database history, manifests | [Repository](formats/repository.md) |
| `src/storage/` | Storage API and backends | [Storage](architecture/storage.md) |
| `src/protocol/` | Messaging, clients/servers, parallel jobs | [Protocol](architecture/protocol.md) |
| `src/db/` | Command-oriented database operations | [Database](architecture/database.md) |
| `src/postgres/` | SQL client and physical format interfaces | [Database](architecture/database.md) |
| `src/common/` | Memory, errors, types, I/O, crypto, compression | [Core runtime](architecture/core-runtime.md) |
| `src/build/` | Generators and authoritative definitions | [Build](development/build.md) |
| `test/src/module/` | C suites grouped by subsystem | [Testing](development/testing.md) |
| `test/src/common/` | Harnesses and assertions | [Writing tests](development/writing-tests.md) |
| `test/src/command/test/` | C-side test generation/build/coverage | [Testing](development/testing.md) |
| `test/lib/` | Perl test job and container support | [CI](development/ci.md) |
| `doc/xml/`, `doc/lib/`, `doc/src/` | Documentation sources and tools | [Build](development/build.md) |
| `arenadata/` | GPDB integration setup and shell tests | [Greenplum](fork/greenplum.md) |

For archive-get, start with `cmdArchiveGet()` and follow `archiveGetCheck()`/`archiveGetFile()`. For a worker parameter change, inspect both the coordinator's pack writer and the handler's reader. For a new option, start in YAML, not generated tables.

```bash
rg -n 'cmdArchiveGet|archiveGetFile' src test/src/module/command
rg -n 'repo-cipher-type' src/build/config/config.yaml src/build/help/help.xml
rg --files src/protocol test/src/common
```

Generated `*.auto.*` files explain compiled behavior but have authoritative inputs elsewhere. The [build page](development/build.md) maps those inputs to outputs.

For narrower implementation contracts, use the [detailed subsystem map](detailed-map.md).

## Source entry points

- [src/main.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/main.c)
- [src/meson.build](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/meson.build)
- [test/define.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/define.yaml)
- [CONTRIBUTING.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/CONTRIBUTING.md)

# pgBackRest developer wiki

[Wiki home](README.md)

This wiki explains the Arenadata pgBackRest fork as a system: its components, data flows, extension points, and tests. Start here and read the pages relevant to a task; no page requires reading the whole wiki first.

**Source baseline:** pgBackRest 2.54.2, `ADBM-3528`, commit `b8ce272231bac178ba39fffc8561b058ab150a0b`, inspected on 2026-11-07. This describes that branch, not the latest upstream release. The separate test-fix commit `c860c049` is documented explicitly below.

## Start here

- [System overview](overview.md): purpose, vocabulary, and recovery lifecycle.
- [Source map](source-map.md): directories and starting points for common tasks.
- [Runtime architecture](architecture/runtime.md): process roles and command dispatch.

## Architecture

- [Configuration](architecture/configuration.md)
- [Storage abstraction](architecture/storage.md)
- [Streaming I/O and filters](architecture/io-filters.md)
- [Protocols and parallel workers](architecture/protocol.md)
- [Database and PostgreSQL interfaces](architecture/database.md)
- [Memory, objects, errors, and logging](architecture/core-runtime.md)

## Workflows

- [Backup](workflows/backup.md)
- [WAL push and get](workflows/wal-archive.md)
- [Restore and recovery](workflows/restore.md)
- [Stanzas, check, info, verify, and expiration](workflows/repository-management.md)

## Repository data

- [Repository layout and metadata](formats/repository.md)
- [Manifests](formats/manifests.md)
- [Bundles and block incremental backups](formats/block-incremental.md)
- [Encryption and passphrase hierarchy](formats/encryption.md)

## Fork-specific behavior

- [Greenplum and Greengage](fork/greenplum.md)
- [Partial restore and WAL filtering](fork/partial-restore.md)
- [ADBM-3528 and its test fixes](fork/adbm-3528.md)

## Development

- [Building and generated code](development/build.md)
- [Test runner and harness architecture](development/testing.md)
- [Writing a test](development/writing-tests.md)
- [Archive-get test walkthrough](development/archive-get-example.md)
- [CI and failure diagnosis](development/ci.md)
- [Making a change](development/change-guide.md)
- [Maintaining this wiki](development/wiki-maintenance.md)
- [Local environment](environment/local.md): workspace-specific paths and historical observations.

## Find context for a task

| Task | Reading route |
|---|---|
| Add an option | Configuration → Build → Writing tests |
| Change repository encryption | Encryption → I/O filters → Protocol → ADBM-3528 |
| Diagnose missing WAL | WAL push/get → Repository layout → Archive-get example |
| Change backup/restore | Relevant workflow → Manifests → Block incremental |
| Fix a C test | Testing → Writing tests → CI |
| Work on GPDB recovery | Greenplum → Partial restore → Database interfaces |

Relative links make this folder usable both on GitHub and after cloning. Source links are pinned to the inspected commit. These are developer notes; operational examples identify their assumptions and do not replace a database recovery plan.

## Source entry points

- [README.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/README.md)
- [src/version.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/version.h)

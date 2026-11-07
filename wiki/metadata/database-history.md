# Database history and archive identifiers

[Wiki home](../README.md)

Scope: distinguishing database incarnations across stanza history. [Repository overview](../formats/repository.md).

`InfoPgData` records a history ID, database system identifier, version, and catalog version. Its control-version field is retained for older format compatibility and is explicitly not a general-purpose current control-version value.

`InfoPg` provides current/history access and archive-ID derivation. Backup/archive metadata each embed the relevant database information and subordinate passphrase state.

## Identity is more than version

Two clusters can have the same PostgreSQL version but different system identifiers. Paths and stanza names can also be reused. Compare the intended current/history identity when accepting WAL or a backup, rather than selecting the newest-looking name.

Stanza creation checks existing metadata against physical control data. Supported stanza-upgrade operations update history intentionally. Backup manifests record the history association needed for later restore/verify.

Verification compares archive and backup history and relates file ranges to appropriate archive IDs. An upgrade adds another interpretation boundary rather than making every old archive part of the current one.

## Tests

Check mismatched system ID, version changes, current/history ordering, and preserved older-format fields. Keep synthetic `pg_control` and info-file fixtures consistent unless the mismatch is the subject of the test.

See [WAL identity](../archive/wal-identity.md), [stanza workflows](../workflows/repository-management.md), and [physical interfaces](../architecture/database.md).

## Source entry points

- [src/info/infoPg.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoPg.h)
- [src/info/infoPg.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoPg.c)
- [src/command/stanza/upgrade.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/stanza/upgrade.c)
- [src/command/check/common.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/check/common.c)
- [src/command/verify/verify.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/verify.c)

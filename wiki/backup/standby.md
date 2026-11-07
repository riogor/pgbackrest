# Backup from a standby

[Wiki home](../README.md)

Scope: selecting sources and coordinating replay. [Backup overview](../workflows/backup.md).

`backupInit()` finds suitable primary/standby database entries through database helpers. Standby backup requires online operation. Preference mode can warn and fall back to primary when no standby is available; strict requirements have different behavior.

## Coordination

The primary still participates in backup coordination. Standby reads need to reach the appropriate replay/checkpoint state and remain on the expected timeline. `dbReplayWait()` and related database operations check progress, timeouts, and whether the selected server is still a standby.

Null replay results, stalled replication, promotion during backup, or a mismatched timeline are not equivalent to ordinary slow disk reads. Errors include context to identify the relevant condition.

## File-source selection

Eligible files may come from standby storage, while control/special paths still require primary handling. `backupProcessFilePrimary()` explicitly selects `global/pg_control` from primary and uses the standby-eligibility expression for other paths. Do not turn standby mode into an unconditional switch of all source reads.

## Tests

Script primary/standby SQL state independently and ensure physical control data agrees. Cover prefer-mode fallback, replay timeout, promotion, and source selection. A general backup success test does not exercise these transitions.

See [database interfaces](../architecture/database.md), [libpq scripts](../testing/pq-scripts.md), and [file pipeline](file-pipeline.md).

## Source entry points

- [src/command/backup/backup.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/backup.c)
- [src/db/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/db/helper.c)
- [src/db/db.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/db/db.c)
- [test/src/module/command/backupTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/backupTest.c)

# Stanzas, checks, verification, and retention

[Wiki home](../README.md)

Repository commands inspect/manage different layers. Their success messages are evidence for different properties.

| Command | Main responsibility |
|---|---|
| `stanza-create` | Initialize metadata for database identity |
| `stanza-upgrade` | Update metadata after a supported database upgrade |
| `stanza-delete` | Remove stanza data, subject to command safeguards |
| `check` | Check database/archive configuration and relevant archiving behavior |
| `info` | Summarize stanza/history/backups/repository status |
| `manifest` | Render detailed backup information |
| `annotate` | Change backup annotations |
| `verify` | Validate metadata and backup/archive contents |
| `expire` | Apply retention and remove expired data |

Stanza creation checks control-file identity against existing metadata rather than assuming a directory is enough. For encrypted repositories it generates subordinate archive and backup passphrases.

Verify loads info/manifests, checks history, and schedules validation of backup/WAL data. It collects missing/checksum/other invalid results for reporting. `check` also concerns live database and archiving configuration; it is not a substitute for repository-wide verification or an actual restore exercise.

Expiration respects dependencies. Expiring a full backup can remove dependent differential/incremental backups. Archive removal must preserve ranges required by retained backups. Change the dependency/range logic rather than implementing simple filename-age deletion.

Lower-level repository commands include `repo-get`, `repo-put`, `repo-ls`, `repo-rm`, and this fork's `repo-push`. They do not replace metadata-aware lifecycle operations.

See [repository layout](../formats/repository.md), [manifests](../formats/manifests.md), and [encryption](../formats/encryption.md). Matching suites live under `test/src/module/command/`.

## Source entry points

- [src/command/stanza/create.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/stanza/create.c)
- [src/command/check/check.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/check/check.c)
- [src/command/verify/verify.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/verify.c)
- [src/command/expire/expire.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/expire/expire.c)
- [src/command/repo/push.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/repo/push.c)

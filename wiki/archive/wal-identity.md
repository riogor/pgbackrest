# WAL names, archive IDs, and timeline history

[Wiki home](../README.md)

Scope: names that select archived data. [WAL workflow](../workflows/wal-archive.md).

A standard WAL segment request has a 24-character hexadecimal name. Archived segment filenames add a content checksum and optional compression extension. Repository grouping also uses an archive ID derived from database history; this separates database incarnations/versions within the stanza.

Archive common code defines regular expressions for segments, partial segments, stored segment names, and timeline history files. These categories should not be interchanged. Timeline history requests are not ordinary segment payloads and do not require every segment-only transformation.

## Identity checks

System identifier and version checks connect database files/SQL state to repository history. A matching stanza name or path alone cannot establish that a WAL file belongs to the intended database. Upgrades/history transitions need metadata support, not simply a new prefix string.

WAL arithmetic depends on version and segment size. Queue planning should use the PostgreSQL interface helpers instead of incrementing name substrings with assumptions about wrap boundaries.

## Tests

Cover segment rollover, history-file requests, supported partial names, and candidates from the correct archive history. For duplicate archive content, distinguish identical bytes from conflicting content under one WAL identity.

See [database history](../metadata/database-history.md), [async get](async-get.md), and [GPDB filtering](../fork/partial-restore.md).

## Source entry points

- [src/command/archive/common.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/common.h)
- [src/command/archive/find.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/find.c)
- [src/postgres/interface.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/postgres/interface.c)
- [src/info/infoPg.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoPg.c)

# Runtime and command dispatch

[Wiki home](../README.md)

`src/main.c` installs stack-trace/memory cleanup handlers, registers storage backends, initializes command timing/statistics/exit handling, and calls `cfgLoad()`. It then dispatches by command and role.

## Roles

Main-role commands coordinate user operations. Async-role archive commands process queues. Local/remote roles serve protocol requests. One executable implements these roles; there is no separate binary for each worker type.

Dispatch calls `cmdBackup()`, `cmdRestore()`, `cmdArchiveGet()`, `cmdVerify()`, and other command entry points. Backup can transition into `expire` after successful completion when automatic expiration is enabled.

Commands commonly separate planning from file work. Archive-get has `get.c` for command logic, `file.c` for retrieving data, and `protocol.c` for worker handling. Backup and restore use a similar division.

## Shared command behavior

- `command/command.c` formats start/end information and redacts secure options.
- `command/lock.c` and `common/lock.c` coordinate operations.
- Start/stop commands manage stanza stop state.
- `command/exit.c` handles termination and cleanup.
- `protocol/helper.c` manages worker/remote clients and transport.

Execution locality matters. `cmdBackup()` verifies repository locality where it executes; `cmdRestore()` verifies PostgreSQL locality. Transport setup may redirect work, so the host where a CLI was typed is not necessarily the host performing every storage operation.

Read [configuration](configuration.md), [protocols](protocol.md), and [core runtime](core-runtime.md) for the supporting contracts.

## Source entry points

- [src/main.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/main.c)
- [src/command/command.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/command.c)
- [src/command/local/local.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/local/local.c)
- [src/command/remote/remote.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/remote/remote.c)

# Protocols and parallel workers

[Wiki home](../README.md)

pgBackRest's client/server protocol supports workers, remote storage, and database operations. Transport details are separated from command-specific handlers and common error reporting.

| Component | Responsibility |
|---|---|
| `protocol/client.c`, `server.c` | Requests, responses, sessions, errors |
| `protocol/helper.c` | Construct/cache clients and choose transport |
| `protocol/parallel.c` | Schedule jobs across clients |
| `protocol/parallelJob.c` | Job identity, parameters, state, results |
| `command/local/local.c` | Register file-worker handlers |
| `command/remote/remote.c` | Register remote-service handlers |

A coordinator supplies a callback to `ProtocolParallel`. It returns a job for a client or null when that client has no more work. Completed results update command state, manifests, or status files. Relevant commands use `process-max` to control parallelism.

## Serialization is an interface

Typed `Pack` writers/readers encode job parameters. A coordinator might write compression type, checksum, cipher type, and passphrase. The handler must read matching types in exactly the same order. Editing one side alone can fail far from the changed call site.

ADBM-3528 adds cipher type to paths that previously assumed AES-256. For such changes follow job callback → handler → file API, including tests for the worker path.

Transport may use local children, SSH, or configured TLS services. Remote storage is a backend using this protocol; SFTP is a separate backend.

Protocol harness shims run real handlers inside controlled child processes connected by pipes. They preserve worker coverage while giving tests predictable setup. See [testing](../development/testing.md).

## Focused guides

- [Session/handler lifecycle](../protocol/sessions.md)
- [Pack fields](../runtime/pack.md)
- [TLS authorization](../network/tls-services.md)


## Source entry points

- [src/protocol/parallel.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/protocol/parallel.h)
- [src/protocol/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/protocol/helper.c)
- [src/command/local/local.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/local/local.c)
- [src/command/restore/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/protocol.c)

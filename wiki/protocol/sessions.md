# Protocol sessions, handlers, and remote errors

[Wiki home](../README.md)

Scope: request lifecycle inside a protocol connection. [Parallel-worker overview](../architecture/protocol.md).

A `ProtocolClient` tracks sessions with numeric session IDs. Multiple sessions share a client connection; a response received for another session can be held until that session consumes it. Do not equate a session with a separate OS process or socket.

## Handler interfaces

`ProtocolServerHandler` identifies a command with a `StringId` and optional open/process/processSession/close callbacks. Stateless work can use a process handler. Stateful operations can initialize session data on open, process later messages against it, and release it on close.

Server results supply packed response data and session behavior. File/storage streaming uses session state rather than requiring every request to describe a complete standalone transfer.

## Errors across the boundary

Client error handling reads error code, message, and stack information, resolves the typed error, prefixes remote context, and throws locally. A message beginning with a local/remote protocol prefix may therefore describe a failure inside a worker rather than a network outage.

When debugging, locate the handler that produced the underlying error before changing transport retries. Check that its pack reader matches the caller's writer and that session data survives until close.

Protocol shims exercise these lifecycles with real handlers. See [Pack](../runtime/pack.md), [remote storage](../storage/remote.md), and [protocol harnesses](../testing/protocol-shims.md).

## Source entry points

- [src/protocol/client.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/protocol/client.c)
- [src/protocol/server.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/protocol/server.h)
- [src/protocol/server.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/protocol/server.c)
- [src/storage/remote/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/remote/protocol.c)

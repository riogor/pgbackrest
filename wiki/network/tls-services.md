# TLS clients and pgBackRest server authorization

[Wiki home](../README.md)

Scope: secure connection setup and stanza authorization. [Protocol overview](../architecture/protocol.md).

The TLS I/O client wraps a lower-level client and supports peer verification, host checking, CA file/path, and optional client certificate/key. Connection and session timeouts are separate constructor inputs. The object is reusable across sessions.

HTTPS object-store traffic and pgBackRest TLS remote services both use common TLS infrastructure, but their application authorization differs.

## pgBackRest service path

`command/server/server.c` creates the listening server/TLS state and accepts connections. `protocolServer()` in `protocol/helper.c` establishes the protocol context and checks authenticated peer identity against `tls-server-auth` data.

The auth map associates a peer name with allowed stanzas. The authorization helper handles a comma-separated list or `*` for all stanzas. Certificate authentication alone does not imply authorization for every stanza. The requested configuration must be read sufficiently to identify the stanza before the application decision.

## Debugging

Distinguish TCP reachability, TLS negotiation, peer verification, client authentication, and stanza authorization. They fail at different points and should not be collapsed into a generic repository password error.

Tests use common server/socket harnesses to create controlled peers and protocol scenarios. Keep certificate handling and application authorization tests separate enough to identify which contract failed.

See [HTTP](http.md), [protocol sessions](../protocol/sessions.md), and [configuration schema](../configuration/schema.md).

## Source entry points

- [src/common/io/tls/client.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/tls/client.h)
- [src/common/io/tls/server.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/tls/server.c)
- [src/command/server/server.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/server/server.c)
- [src/protocol/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/protocol/helper.c)

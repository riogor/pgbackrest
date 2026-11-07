# SFTP sessions and host-key checks

[Wiki home](../README.md)

Scope: libssh2-backed storage, distinct from pgBackRest's SSH remote protocol. [Storage overview](../architecture/storage.md).

The SFTP backend opens a network/SSH session, verifies the host according to configuration, authenticates the user, initializes SFTP, and exposes filesystem-like operations through `Storage`. It is compiled when optional libssh2 support is available.

## Trust and authentication

Host fingerprint verification and known-hosts checking are alternatives controlled by backend options. Known-host handling loads configured/default files and interprets the configured checking policy. Server identity verification is different from authenticating the connecting user.

Incorrect keys, failed host verification, and remote path permissions should produce distinct diagnoses. Do not solve a permissions or fingerprint mismatch by changing backup cipher settings.

## Nonblocking library behavior

libssh2 operations can require waiting/retrying when the socket is not ready. Follow the backend's wait/error helpers rather than adding raw repeated calls that ignore direction/readiness and timeouts. Resource cleanup must consider SFTP handles and the underlying SSH session.

`harnessLibSsh2` scripts library behavior for tests, including failures impractical to reproduce reliably against a live service. Integration with actual servers remains a separate confidence layer.

See [remote storage](remote.md) for the pgBackRest protocol alternative, [timeouts](../runtime/errors-retry.md), and [storage tests](../testing/storage-fixtures.md).

## Source entry points

- [src/storage/sftp/storage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/sftp/storage.c)
- [src/storage/sftp/read.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/sftp/read.c)
- [src/storage/sftp/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/sftp/write.c)
- [test/src/common/harnessLibSsh2.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessLibSsh2.h)
- [test/src/module/storage/sftpTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/storage/sftpTest.c)

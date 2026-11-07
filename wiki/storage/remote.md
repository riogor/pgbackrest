# Remote storage and filter execution

[Wiki home](../README.md)

Scope: storage operations delegated to another pgBackRest process. [Storage overview](../architecture/storage.md).

`storage/remote/` implements the same storage API as local/object backends but sends operations through protocol clients. Its server handlers operate on the remote host's configured storage. This differs from the SFTP backend's direct libssh2 file operations.

## Streaming state

A remote reader owns a protocol session, current data block, remaining-byte state, and EOF state. EOF may already be known while unread bytes remain in a buffered block. Code that equates a received EOF flag with no remaining output can truncate a read.

Remote read/write requests carry options and supported filter descriptions. A remote side can process filters and return results rather than transmitting all logical file bytes. Verify's sink/hash/size pipeline is an example of why filter placement matters for network use.

## Configuration and errors

Storage helpers select remote objects using host configuration. Child/remote parameters must carry effective options and relevant secrets safely. Remote metadata access can establish encryption settings, so commands must not read stale defaults before initializing the required storage path.

Reported errors often include a remote protocol prefix but preserve the underlying typed failure. Inspect server storage/codec/permission errors before assuming transport broke.

Test streaming boundaries, ignored-missing files, offsets/limits, and filter results. See [sessions](../protocol/sessions.md), [Pack](../runtime/pack.md), and [verification](../integrity/verification.md).

## Source entry points

- [src/storage/remote/read.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/remote/read.c)
- [src/storage/remote/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/remote/write.c)
- [src/storage/remote/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/remote/protocol.c)
- [src/storage/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/helper.c)
- [src/command/verify/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/file.c)

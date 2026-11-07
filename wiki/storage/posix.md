# POSIX write durability and the CIFS variant

[Wiki home](../README.md)

Scope: filesystem write completion. [Storage overview](../architecture/storage.md).

A POSIX storage writer owns a file descriptor, temporary/final names, and write options. Open behavior includes creation and conditional truncation. Parent-directory creation, permissions, ownership, and metadata follow configured storage options rather than being implicit universal guarantees.

## Close sequence

`storageWritePosixClose()` conditionally fsyncs the file, clears its resource callback, closes the descriptor, applies requested modification time, renames a temporary file when atomic mode is enabled, and conditionally syncs the containing path.

Atomic rename, file sync, and directory sync solve different problems. Rename avoids exposing an incomplete final pathname; syncing concerns durability. A successful series of writes is not the same completion boundary as successful close/rename/sync.

An error/unwind callback closes a descriptor left open by an interrupted operation. Clearing the callback on normal close prevents a second close later.

## CIFS

The CIFS constructor reuses the internal POSIX storage implementation with a different capability/configuration choice. It is not a separate SMB network client implemented in this tree. Mounted-filesystem behavior and backend capabilities still matter.

Tests should cover failures during close, rename, sync, and existing-file handling, not only byte writes. See [memory callbacks](../runtime/memory-contexts.md) and [storage fixtures](../testing/storage-fixtures.md).

## Source entry points

- [src/storage/posix/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/posix/write.c)
- [src/storage/posix/storage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/posix/storage.c)
- [src/storage/cifs/storage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/cifs/storage.c)
- [test/src/module/storage/posixTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/storage/posixTest.c)

# Storage abstraction and backends

[Wiki home](../README.md)

Commands use `Storage` rather than implementing filesystem or HTTP operations directly. The interface provides reads/writes, listing/info, copies, removal, and supported path/link operations. Storage read/write objects expose streaming `IoRead`/`IoWrite` interfaces.

## Layers

- `storage/storage.c` validates and delegates common operations.
- `read.c`, `write.c`, `iterator.c`, and `list.c` implement shared objects.
- `posix/` handles local files; `cifs/` provides a filesystem-oriented variant.
- `s3/`, `azure/`, and `gcs/` implement object-store repositories.
- `sftp/` uses optional libssh2 support.
- `remote/` performs operations through pgBackRest's protocol.

Backends declare capabilities with `StorageFeature`. Object-store prefixes are not POSIX directories. Hard links, symlinks, path syncing, detailed modes, and versioning are not universally available. Check capabilities before introducing an assumption in common code.

## Configured helpers

`storage/helper.c` constructs/caches objects from configuration. `storagePgIdx(0)` and `storageRepoIdx(0)` select internal group entries. `storagePg()`/`storageRepo()` use the selected/default entry. Writable access is explicit through `*Write()` variants.

`<REPO:ARCHIVE>` and `<REPO:BACKUP>` are internal expressions expanded into stanza-aware paths, not literal directory names. Helpers also expose spool storage and initialize dry-run write restrictions.

Reloading configuration can invalidate cached storage. The test harness calls `storageHelperFree()` before loading options; borrowed pointers must not survive their owner.

See [I/O filters](io-filters.md) for byte transformations and [repository layout](../formats/repository.md) for persisted paths. Backend suites are under `test/src/module/storage/`.

## Focused guides

- [POSIX/CIFS](../storage/posix.md)
- [S3](../storage/s3.md)
- [Azure](../storage/azure.md)
- [GCS](../storage/gcs.md)
- [SFTP](../storage/sftp.md)
- [Remote protocol storage](../storage/remote.md)
- [HTTP requests](../network/http.md)


## Source entry points

- [src/storage/storage.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/storage.h)
- [src/storage/storage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/storage.c)
- [src/storage/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/helper.c)
- [src/storage/remote/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/remote/protocol.c)

# Repository layout and metadata

[Wiki home](../README.md)

Repositories contain backed-up bytes plus metadata describing database identity, backup ancestry, and WAL history. Storage helpers expand stanza-aware path expressions.

A simplified physical layout is:

```text
<repo>/
  archive/<stanza>/
    archive.info
    archive.info.copy
    <archive-id>/...WAL and history files...
  backup/<stanza>/
    backup.info
    backup.info.copy
    <backup-label>/
      backup.manifest
      ...file data and/or bundles...
```

The diagram omits transient files, optional links, archive subdirectories, and compression extensions. On object stores these paths are key prefixes.

## Metadata ownership

- `Info`: common serialization/checksum and load/retry support.
- `InfoPg`: current/historical database identities and subordinate passphrases.
- `InfoArchive`: archive database history and archive identifiers.
- `InfoBackup`: completed backups, references, ancestry, timing, WAL bounds, sizes, options.
- `Manifest`: detailed contents and locations for one backup.

`.copy` files provide alternate load candidates. If neither is readable, loading aggregates errors and preserves useful failure types. Metadata checksums detect invalid content independently of file existence. Supported reconstruction paths in `infoBackupLoadFileReconstruct()` reconcile backup information against manifests.

`REPOSITORY_FORMAT` equals 5 at this baseline. It is a format compatibility boundary, not the software version. Persistent-format edits require compatibility analysis for both readers and writers.

See [manifests](manifests.md), [block incremental](block-incremental.md), and [encryption](encryption.md). This layout is explanatory; do not manually edit repository metadata based on the diagram.

## Focused guides

- [Info-file loading](../metadata/info-loading.md)
- [Database history](../metadata/database-history.md)


## Source entry points

- [src/version.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/version.h)
- [src/storage/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/helper.c)
- [src/info/info.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/info.c)
- [src/info/infoPg.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoPg.h)
- [src/info/infoBackup.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoBackup.h)
- [src/info/infoArchive.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoArchive.h)

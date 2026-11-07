# Backup manifests

[Wiki home](../README.md)

`backup.info` catalogs completed backups. A `backup.manifest` describes one backup in detail: its logical contents, attributes, references, and stored locations.

## Data model

`ManifestData` includes label/type, prior/reference information, database/version identity, timestamps, WAL/LSN boundaries, and backup options. Other structures describe files, directories, symbolic links, targets such as `pg_data`/tablespaces, ownership/modes, sizes, and checksums.

Files may reference an earlier backup, occupy offsets inside bundles, or require block maps. A logical database path therefore does not necessarily map to a standalone object in the current backup directory.

## Lifecycle

`manifestNewBuild()` creates the initial description. Backup comparison/resume logic marks work and references. Worker results fill actual hashes, sizes, and encoding locations. In-progress copies preserve state; completion records final metadata and validates the finished representation.

Restore loads and validates a manifest, maps targets, and queues required data. Verify derives expected files/checksums from it. The manifest command renders it for inspection, including supported block-delta details.

## Changing a field

Consider parser/save behavior, defaults for older manifests, validation, rendering, backup construction, and restore/verify consumers. Optional or version-dependent values must not be treated as unconditionally present. The data subpassphrase also belongs to manifest state; encryption is more than a property of stored filenames.

`info/manifestTest.c` covers serialization/build behavior; command tests cover consumers. See [block incremental data](block-incremental.md) for location fields and [encryption](encryption.md) for passphrase levels.

## Focused guides

- [Ancestry/resume](../backup/ancestry-resume.md)
- [File-worker measurements](../backup/file-pipeline.md)


## Source entry points

- [src/info/manifest.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/manifest.h)
- [src/info/manifest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/manifest.c)
- [src/command/manifest/manifest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/manifest/manifest.c)
- [test/src/module/info/manifestTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/info/manifestTest.c)

# What each checksum validates

[Wiki home](../README.md)

Scope: separating several different integrity/change-detection mechanisms. [Filter overview](../architecture/io-filters.md).

| Mechanism | Data/property |
|---|---|
| Logical file SHA-1 | Backup/restored logical file contents |
| Repository-byte hash | Stored representation after applicable transformations |
| Info-file checksum | Serialized metadata content |
| PostgreSQL page checksum | Physical relation pages using database rules |
| Block xxHash | Detecting changed blocks for incremental representation |
| Request signing/HMAC | Service authentication/request integrity conventions |

These values are not interchangeable. In particular, a compressed/encrypted byte stream has a different hash/size from its decoded file. Filter placement determines which one is measured.

## Page validation

`pageChecksumNew()` receives segment/page parameters and validates relation pages. It handles page/header validity and alignment, including all-zero/new-page behavior. Invalid-page details are returned for reporting; page-checksum warnings in a backup are distinct from an overall file hash mismatch.

Page size and relation-segment numbering are inputs, not universal constants. Fork-specific physical formats can differ.

## Testing

Use deliberately wrong logical bytes, wrong stored bytes, malformed pages, and changed block maps to test the corresponding check. Do not claim that a file-size assertion validates content. A successful metadata checksum says nothing by itself about every referenced payload.

See [backup file pipeline](../backup/file-pipeline.md), [verification](verification.md), [block incremental](../formats/block-incremental.md), and [cipher lifecycle](../crypto/cipher-lifecycle.md).

## Source entry points

- [src/common/crypto/hash.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/hash.h)
- [src/common/crypto/xxhash.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/xxhash.h)
- [src/command/backup/pageChecksum.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/pageChecksum.c)
- [src/command/backup/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/file.c)
- [src/info/info.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/info.c)

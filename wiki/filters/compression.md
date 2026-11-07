# Compression filters and encoded filenames

[Wiki home](../README.md)

Scope: selecting codecs and preserving their streaming contract. [I/O-filter overview](../architecture/io-filters.md).

`common/compress/helper.c` maps compression types to names/extensions and constructs compress/decompress filters. Implementations live under codec-specific directories for gzip, bzip2, lz4, and zstd; build features determine which optional libraries are present.

## Representation and transport

Repository compression changes persisted bytes and metadata sizes. Network compression is a transport optimization and should not be inferred to change a repository file's declared encoding. Commands carry the relevant type explicitly where filenames alone are insufficient, such as bundle/block data.

Filename-extension helpers are useful when selecting an archive decoder, but arbitrary suffix inspection is not a replacement for manifest metadata in every path. A filename that suggests compression can still contain malformed/truncated bytes.

## Streaming

Compression can buffer output and require final flushing. Decompression must report incomplete/invalid streams rather than silently accepting every EOF. Respect `inputSame()`/`done()` and test small buffers, empty input, and invalid content.

Block incremental super blocks can be compressed separately, making logical file size unrelated to one compressed object's boundaries. Avoid applying a whole-file decoder to an arbitrary bundle offset without the correct limit/framing.

See [block representation](../formats/block-incremental.md), [backup pipeline](../backup/file-pipeline.md), and [WAL reads](../workflows/wal-archive.md).

## Source entry points

- [src/common/compress/helper.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/compress/helper.h)
- [src/common/compress/helper.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/compress/helper.c)
- [src/common/compress/gz/decompress.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/compress/gz/decompress.c)
- [src/command/archive/get/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/file.c)

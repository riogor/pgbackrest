# Streaming I/O and filters

[Wiki home](../README.md)

`IoRead`/`IoWrite` connect sources and sinks through reusable filter groups. A copy can calculate hashes, compress, encrypt, and count bytes without holding the complete file in memory. Filter order depends on the caller: logical-content and stored-byte checksums describe different data.

## Contract

`IoFilterInterface` supports **In** filters, which observe bytes and return results without transforming input, and **InOut** filters, which produce transformed output.

InOut filters must handle output capacity smaller than the transformed data. `inputSame()` requests that the caller present the same input again with fresh output space. `done()` reports whether processing/flushing is complete. Null input flushes buffered output, including compression endings and cipher final blocks.

A filter has a type and packed constructor parameters. Supported filters can be reconstructed on another side of a protocol connection. Results are packs too, allowing callers to collect hashes or sizes after copying.

## Implementations and ownership

Compression is under `common/compress/`, crypto/hash under `common/crypto/`, and generic buffer/size filters under `common/io/filter/`. The block incremental filter lives with backup because it implements repository representation.

Freeing an `IoFilter` frees its owned driver. `ioFilterDriver()` returns a borrowed pointer, not an independently owned object. The GOST test use-after-free came from reading a driver after freeing its filter.

Cover empty input, partial headers, small output buffers, repeated input, and final flushes in filter tests. See [encryption](../formats/encryption.md), [block incremental data](../formats/block-incremental.md), and [core runtime](core-runtime.md).

## Source entry points

- [src/common/io/filter/filter.intern.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/filter/filter.intern.h)
- [src/common/io/filter/group.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/filter/group.c)
- [src/common/io/read.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/read.c)
- [src/common/io/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/write.c)

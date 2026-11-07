# Buffers, strings, and compact identifiers

[Wiki home](../README.md)

Scope: three types frequently confused in streaming and configuration code. [Core overview](../architecture/core-runtime.md).

`Buffer` holds binary bytes with separate allocated capacity, reported size/limit, and used length. After writing through `bufPtr()`, the caller is responsible for updating used length. Filling memory without updating it can make subsequent I/O see an empty or shorter buffer.

`bufSize()` describes available size; `bufUsed()` describes content. Tests for encrypted payloads often allocate capacity, fill bytes, then set used length before passing the buffer to a filter. A limited buffer may present less space than was actually allocated.

`String` represents text and provides length-aware construction/comparison/manipulation. Use explicit conversions for binary data rather than assuming arbitrary file bytes are a C string. `strZ()` exposes a C-string view whose lifetime remains tied to the string owner.

## StringId

`StringId` packs a restricted text identifier into an integer. It is useful for command/filter/enum identifiers and compact protocol fields, but it cannot represent arbitrarily long names losslessly.

ADBM-3528 uses explicit cipher-ID/name mapping for long names. Parsing additionally checks original text against allowed values so an ID collision/truncation does not silently accept a misspelling.

Review both representation and lifetime when converting among these types. A returned C pointer or buffer driver is not a new ownership transfer.

See [cipher mapping](../crypto/cipher-lifecycle.md), [Pack](pack.md), and [memory contexts](memory-contexts.md).

## Source entry points

- [src/common/type/buffer.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/buffer.h)
- [src/common/type/string.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/string.h)
- [src/common/type/stringId.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/stringId.h)
- [src/config/parse.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/parse.c)
- [src/common/crypto/cipherBlock.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/cipherBlock.c)

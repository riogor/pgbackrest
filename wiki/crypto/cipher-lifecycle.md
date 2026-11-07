# Cipher lookup, ownership, and stream finalization

[Wiki home](../README.md)

Scope: `CipherBlock` as an OpenSSL-backed filter. [Encryption overview](../formats/encryption.md).

Cipher IDs map explicitly to OpenSSL names because long names do not fit compact identifiers losslessly. Configuration compatibility normalization chooses the appropriate Kuznyechik/Grasshopper spelling, while constructor lookup also handles supported fallback behavior.

## Ownership

On OpenSSL 3, `EVP_CIPHER_fetch()` returns an owned object. Name/engine lookup returns a borrowed object. `CipherBlock` tracks the fetched pointer separately so cleanup frees only owned state. Its cipher context is also an external resource owned by the filter driver.

Resource callbacks must cover early construction/processing failure as well as successful finalization. When processing creates/updates resource state, callback registration must not double-register or lose the owner.

## Framing and flush

Encryption emits salt/header state, derives key/IV, processes input, and flushes the final block. Decryption may receive partial headers and must wait until framing is available. Raw mode changes the magic-prefix convention; both sides still need matching framing, cipher, passphrase, and digest assumptions.

`inputSame()` and `done()` support limited output buffers and final flushes. Empty input still has encoded framing/padding behavior. Test these boundaries for different cipher block sizes rather than hard-coding an AES-sized expectation.

A driver pointer remains borrowed from its `IoFilter`. The GOST test fix reads the current encryption driver for its size assertion instead of a freed decryption driver.

See [I/O contract](../architecture/io-filters.md), [memory contexts](../runtime/memory-contexts.md), and [checksum purposes](../integrity/checksums.md).

## Source entry points

- [src/common/crypto/cipherBlock.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/cipherBlock.c)
- [src/common/crypto/common.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/common.h)
- [src/config/load.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/load.c)
- [test/src/module/common/cryptoGostTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/common/cryptoGostTest.c)

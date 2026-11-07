# Info-file checksums, fallback, and error context

[Wiki home](../README.md)

Scope: loading metadata when a primary or copy may be damaged. [Repository overview](../formats/repository.md).

`Info` supplies common parsing/checksum behavior. Archive, backup, and manifest loaders add domain fields and storage/cipher handling. A successfully opened file can still fail decryption, parsing, checksum validation, or domain validation.

## Fallback

`infoLoad()` drives callback-based candidate loading, usually primary then copy. It collects failures so the final error can explain both attempts. A failed first candidate is not automatically a failed operation if a later candidate loads successfully.

Preserve the distinction between missing files and present-but-invalid files. Failure aggregation deliberately selects useful overall error context rather than blindly returning the last low-level error.

## Encrypted diagnostics

ADBM-3528's `infoLoadErrorRethrow()` preserves the original error, then probes a known plaintext prefix after decryption. Wrong prefix yields a cipher/passphrase hint; a matching prefix adds possible-corruption context. Short/unreadable prefixes retain original error behavior as appropriate.

This additional probe can itself fail and must not accidentally replace the original useful type/message with an unrelated exception. Prefix success is not whole-file validation.

## Tests

Cover missing/missing, corrupt/valid-copy, valid-primary, wrong cipher/passphrase, truncated content, and invalid checksums. Test successful fallback without stale failure logging as well as final aggregated errors.

See [database history](database-history.md), [encryption](../formats/encryption.md), and [error preservation](../runtime/errors-retry.md).

## Source entry points

- [src/info/info.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/info.c)
- [src/info/infoArchive.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoArchive.c)
- [src/info/infoBackup.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/infoBackup.c)
- [src/info/manifest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/manifest.c)
- [test/src/module/info/infoTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/info/infoTest.c)

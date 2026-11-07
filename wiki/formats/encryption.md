# Repository encryption and passphrases

[Wiki home](../README.md)

Repository encryption is a streaming transformation. `repo-cipher-type` selects an algorithm; `repo-cipher-pass` protects top-level encrypted metadata. Changing the option does not convert an existing repository.

## Passphrase hierarchy

```text
configured repository passphrase
  ├─ archive.info → generated archive passphrase → WAL
  └─ backup.info  → generated backup passphrase  → manifests
                                  manifests → data subpassphrase → backup data
```

Stanza creation generates archive and backup subordinate passphrases. Backup creates or carries its data subpassphrase according to ancestry. Distinct levels have distinct passphrases; the configured cipher type must still propagate through all relevant file and worker paths.

## OpenSSL/filter implementation

`cipherBlockNewP()` creates an I/O filter. It derives key/IV using `EVP_BytesToKey`, with SHA-1 as its default digest. Normal framing uses `Salted__` plus salt. Raw mode omits the magic prefix while retaining this implementation's salt convention.

OpenSSL 3 first attempts `EVP_CIPHER_fetch()` and falls back to name/engine lookup. Only fetched cipher objects are owned/freed. Earlier versions use name lookup. Configuration normalization handles Kuznyechik/Grasshopper naming differences.

ADBM-3528 adds AES-128/192, ARIA variants, SM4, GOST Kuznyechik/Grasshopper, and Magma alongside AES-256. Accepting a config value does not install a cipher: runtime providers/engines and OpenSSL configuration determine availability.

## Diagnostics

After a metadata load failure, `infoLoadErrorRethrow()` tries decrypting a known prefix. This improves wrong-cipher/passphrase errors and adds corruption context when the prefix decrypts. It is a diagnostic heuristic, not proof the complete file is correct.

See [ADBM-3528](../fork/adbm-3528.md) for changed paths and [testing](../development/testing.md) for the separate enabled/disabled GOST configurations.

## Source entry points

- [src/common/crypto/cipherBlock.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/cipherBlock.c)
- [src/common/crypto/common.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/crypto/common.c)
- [src/command/stanza/create.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/stanza/create.c)
- [src/info/info.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/info/info.c)
- [src/config/load.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/load.c)

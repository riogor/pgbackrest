# Storage, info, and physical-data fixtures

[Wiki home](../README.md)

Scope: constructing files that production readers can actually consume. [Writing-tests overview](../development/writing-tests.md).

`HRN_STORAGE_PUT` writes binary fixture content; `_Z` writes text and `_EMPTY` creates empty files. Optional compression/encryption parameters let fixtures match repository representation. These are real test files under configured scratch paths, not an in-memory map that automatically implements every filesystem behavior.

`HRN_INFO_PUT` adds the metadata checksum through info helpers before writing. Handwritten info text can therefore focus on domain sections without manually maintaining a checksum after each edit.

`HRN_PG_CONTROL_PUT` generates version-aware control data; WAL helpers populate appropriate synthetic headers. Keep system identifier, version, page size, and segment size consistent with the command's expected identity.

## Isolation

New `testBegin()` runs clear prior filesystem fixtures. Loop iterations within a run do not. Reusing a repository with another cipher requires replacing metadata or selecting another path; merely overwriting one WAL payload is insufficient.

`HRN_CFG_LOAD` clears cached storage objects. Create/use configured storage after loading the intended arguments, and avoid retaining freed helpers across reloads.

## Assertions

Check contents, listings, sizes, and absence as appropriate. `.remove=true` options can perform cleanup but must be understood individually. Use `.noErrorOnExists` only when directory reuse is intentional; otherwise let accidental reuse fail.

See [metadata fallback](../metadata/info-loading.md), [WAL identity](../archive/wal-identity.md), and [archive-get example](../development/archive-get-example.md).

## Source entry points

- [test/src/common/harnessStorage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessStorage.c)
- [test/src/common/harnessInfo.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessInfo.h)
- [test/src/common/harnessPostgres.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessPostgres.h)
- [test/src/common/harnessConfig.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessConfig.c)

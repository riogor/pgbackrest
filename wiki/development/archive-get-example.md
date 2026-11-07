# Archive-get test walkthrough

[Wiki home](../README.md)

Read `test/src/module/command/archiveGetTest.c` beside `command/archive/get/get.c`. Its five top-level runs cover `queueNeed()`, async get, main get, GOST get, and WAL filtering. The GOST fixes described here are in the [test-fix commit](https://github.com/riogor/pgbackrest/blob/c860c049bbd97e631a657db79c584774174f135b/test/src/module/command/archiveGetTest.c), not the original baseline.

## Queue planning

The first run loads stanza, async, PostgreSQL-path, and spool-path options. It calls internal `queueNeed()` directly: a missing directory must raise `PathMissingError`; after directory creation, tests compare the exact needed segment list for different queue contents/capacities.

This requires no live database/worker. The generated translation unit makes the static helper visible to the test.

## Encrypted command flow

The GOST run writes `archive.info` with `HRN_INFO_PUT`, database history for version 10, and an archive subpassphrase. `HRN_STORAGE_PUT` encrypts a synthetic archived payload using that subordinate passphrase.

Arguments select stanza/repository/cipher and add request/destination positional parameters. The top-level passphrase comes from an environment helper. The corrected action/assertion section includes:

```c
HRN_STORAGE_PATH_CREATE(storagePgWrite(), "pg_wal", .noErrorOnExists = true);

TEST_RESULT_INT(cmdArchiveGet(), 0, "get");
TEST_RESULT_LOG(
    "P00   INFO: found 01ABCDEF01ABCDEF01ABCDEF in the repo1: 10-1 archive");

TEST_RESULT_UINT(
    storageInfoP(storagePg(), STRDEF("pg_wal/RECOVERYXLOG")).size,
    16 * 1024 * 1024, "check size");
```

The suite also lists/removes the destination and removes archived payloads between ciphers. The size assertion checks the restored size; it alone does not prove full content equality. Add a content assertion when that stronger property is the change's intended contract.

## Workers and logs

Async tests install `PROTOCOL_SERVER_HANDLER_ARCHIVE_GET_LIST` through `hrnProtocolLocalShimInstall()`. It forks children, uses pipes, and runs actual handlers. Tests inspect `.ok`/`.error` files, protocol errors, and process-prefixed logs. Explicit fork synchronization handles lock/timeouts.

`TEST_RESULT_LOG()` consumes output. Unchecked success messages fail at the next run/end even if the command returned zero. This explained the second archive-get failure after creating its missing output path.

## Focused run

From outside the checkout, with dependencies activated:

```bash
/path/to/pgbackrest/test/test.pl --vm=none --module=command \
  --test=archive-get --run=4 --no-coverage --no-valgrind \
  --test-path=/path/to/disposable-test-work
```

Choose the corrected checkout to run the fixed version. `--run=4` is the GOST block at this baseline. See [local environment](../environment/local.md) for prepared paths and [writing tests](writing-tests.md) for fixture conventions.

## Source entry points

- [test/src/module/command/archiveGetTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/archiveGetTest.c)
- [test/src/common/harnessProtocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessProtocol.c)
- [src/command/archive/get/get.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/get.c)
- [src/command/archive/get/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/archive/get/file.c)

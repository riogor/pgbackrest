# Writing a C test

[Wiki home](../README.md)

A suite defines `static void testRun(void)` with `FUNCTION_HARNESS_VOID()`/`FUNCTION_HARNESS_RETURN_VOID()` and `if (testBegin("description"))` blocks. Order determines `--run=N`; adding a top-level block requires updating `total` in `test/define.yaml`.

## Arrange, execute, assert

1. Build arguments and load the command/role through `HRN_CFG_LOAD`.
2. Create fixtures under `TEST_PATH` using configured storage.
3. Call production code directly or exercise its protocol path.
4. Check the result/error, logs, and persisted effects.

| Helper | Purpose |
|---|---|
| `TEST_RESULT_INT/BOOL/STR_Z/...` | Evaluate/compare typed values |
| `TEST_RESULT_VOID` | Complete without throwing |
| `TEST_ERROR` | Match an error type and message |
| `TEST_RESULT_LOG` | Compare and consume captured output |
| `HRN_STORAGE_PUT[_Z/_EMPTY]` | Create fixture data |
| `TEST_STORAGE_GET/LIST/EXISTS` | Inspect persisted state |
| `HRN_INFO_PUT` | Write checksummed, optionally encrypted metadata |
| `HRN_PG_CONTROL_PUT` | Generate valid physical control data |

Read helper semantics before relying on cleanup. For example, removing listed files is not necessarily removing their containing directory. Path helpers can deliberately reject existing paths unless allowed.

## Common mistakes

Files do not survive top-level run cleanup. They do survive loop iterations inside one run, so a different cipher cannot read prior ciphertext without recreating/isolation. Configuration reload frees cached storage. Passphrases commonly need environment helpers. Logs—including normalization warnings—must be asserted.

Drivers borrowed from filters cannot outlive those filters. Test empty/flush/small-buffer cases for streaming work. If a pack layout changes, test coordinator/handler serialization together rather than only the final file function.

Extend the nearest suite with minimal structurally valid fixtures. Use `TEST_TITLE()` for a scenario sharing existing setup; a new `testBegin()` is appropriate for independent setup. See the [archive-get walkthrough](archive-get-example.md) and [CI](ci.md).

## Source entry points

- [test/src/common/harnessTest.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessTest.h)
- [test/src/common/harnessStorage.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessStorage.h)
- [test/src/common/harnessConfig.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessConfig.c)
- [test/src/common/harnessLog.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessLog.c)
- [test/src/common/harnessInfo.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessInfo.h)

# Prepared environment and historical observations

[Wiki home](../README.md)

This page describes paths seen in the prepared cloud workspace on 2026-11-07 and historical validation from this conversation. The environment was subsequently replaced: prior uncommitted wiki/investigation files did not carry over. Recheck paths, branch state, binaries, and services before using these notes as execution context.

## Prepared locations

| Path | Purpose |
|---|---|
| `/workspace/pgbackrest` | Source snapshot on `ADBM-3528` at `b8ce27223` during recreation |
| `/workspace/pgbackrest/src/pgbackrest` | Prepared Make-built executable in the original setup |
| `/workspace/pgbackrest-env` | User-local dependencies, GOST setup, builds, tests/logs |
| `/workspace/greengage` | Greengage source |
| `/workspace/greengage-build`, `/workspace/greengage-install` | Build and installation |
| `/workspace/greengage-env` | Activation, data/socket, smoke backup/config, startup helper |

The prepared activation route is:

```bash
. /workspace/greengage-env/activate
/workspace/pgbackrest/src/pgbackrest version
openssl version
```

Originally it supplied dependency paths, Greengage tools, and GOST-enabled `OPENSSL_CONF`. `openssl-no-gost.cnf` supported negative crypto tests. Ordinary crypto deliberately expects GOST to be absent in some cases; cryptoGost requires enabled ciphers.

## Historical smoke setup

The `smoke` stanza used port 7543, a Unix socket, `fork=GPDB`, and utility-session SQL access. `start.sh` could initialize/start a local utility instance and run stanza-create/check. It changes state, so inspect it first.

At the original inspection the database was stopped and an existing full backup reported `status: ok`. Earlier server output identified Greengage 7 beta based on PostgreSQL 12.22; OpenSSL reported 3.5.7 with a working GOST engine. These facts are not a fresh service/recovery validation.

## Historical test evidence

The corrected archive-get/archive-push/check suites passed without coverage/Valgrind. Corrected cryptoGost also passed with Valgrind after local tool/debug-symbol setup. Modified-file formatting and `git diff --check` passed. The full CI matrix and OpenSSL 1.1 runtime were not rerun.

The fixes are durably available on GitHub as `ADBM-3528-test-fixes` at `c860c049`. The former `/workspace/work/ci-investigation` worktree was temporary and should not be assumed to exist. Clone/check out the branch when needed.

Test scratch is disposable and can be cleared by the runner. Run `test.pl` outside its own source checkout. See [archive-get example](../development/archive-get-example.md) and [ADBM-3528](../fork/adbm-3528.md).

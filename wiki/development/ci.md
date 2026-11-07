# CI and failure diagnosis

[Wiki home](../README.md)

`.github/workflows/test.yml` defines the main Actions matrix. At this baseline, push/PR triggers match integration and CI branch patterns, not every arbitrary feature branch. Publishing a feature/docs branch does not imply the matrix ran.

## Jobs

- Debian 11/i386 functional tests without performance, coverage, or Valgrind.
- Ubuntu 22 coverage tests with an alternate timezone.
- Ubuntu 22 Valgrind tests with reduced other instrumentation.
- Fedora 43 C tests for newer compiler/toolchain behavior.
- Ubuntu/RHEL integration and documentation builds.
- Emulated ppc64le/s390x checks, including big-endian behavior.
- Meson unity/exported-symbol checks, formatting, and CodeQL.

`test/ci.pl` performs setup and invokes `test/test.pl`. `ContainerTest.pm` generates container setup; `VmTest.pm` defines targets/properties. A VM name generally refers to a test container environment here.

## Diagnosing a failure

Identify the exact commit/job, then the first actual compile/assertion/Valgrind error. Separate setup/package failures, functional mismatches, unexpected logs, generation/formatting failures, coverage gaps, and memory errors.

Reproduce the smallest suite/run before broadening to neighboring changed paths. `--no-coverage`/`--no-valgrind` isolate functional work but do not replace those checks. A successful normal invocation followed by a Valgrind error is still a failure.

GOST setup is version-dependent. Containers build an engine and retain a no-GOST config for missing-cipher tests. Globally enabling GOST can invalidate those negative tests. Documentation jobs execute examples and test behavior beyond text rendering.

Record the scope of local checks and distinguish them from matrix results. The [ADBM-3528 page](../fork/adbm-3528.md) gives a concrete historical example; [testing](testing.md) explains runner layers.

## Source entry points

- [.github/workflows/test.yml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/.github/workflows/test.yml)
- [test/ci.pl](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/ci.pl)
- [test/lib/pgBackRestTest/Common/ContainerTest.pm](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/lib/pgBackRestTest/Common/ContainerTest.pm)
- [test/lib/pgBackRestTest/Common/VmTest.pm](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/lib/pgBackRestTest/Common/VmTest.pm)
- [test/lib/pgBackRestTest/Common/JobTest.pm](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/lib/pgBackRestTest/Common/JobTest.pm)

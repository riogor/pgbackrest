# Testing workers with protocol shims

[Wiki home](../README.md)

Scope: exercising child-process handlers without launching the normal worker executable. [Harness overview](../development/testing.md).

`hrnProtocolLocalShimInstall()` registers the handler set the test needs. The shim intercepts the local worker-launch path, creates pipes, forks a child, prepares its config, and calls the real protocol server with those handlers.

The child clears inherited protocol clients carefully so it does not accidentally send exit messages or kill a server belonging to the parent. It reloads the appropriate worker arguments and sets a process ID for recognizable logs.

## What is real

Coordinator packing, pipe I/O, session/handler dispatch, worker file functions, and error propagation still execute. The replaced boundary is process launch/setup. This gives more coverage than replacing the file operation with a canned return value.

Remote shims serve related needs for remote handler paths. Explicit fork helpers supply parent/child notifications in lock and race-sensitive scenarios.

## Test discipline

Install only the relevant handlers and uninstall when a scenario should return to ordinary launching. Synchronize process state with notifications rather than timing guesses. Assert returned error context and status files as well as the main result.

If a test unexpectedly needs the binary or attempts a network connection, inspect shim registration, role, and handler list first. See [sessions](../protocol/sessions.md), [Pack](../runtime/pack.md), and [archive-get example](../development/archive-get-example.md).

## Source entry points

- [test/src/common/harnessProtocol.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessProtocol.h)
- [test/src/common/harnessProtocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessProtocol.c)
- [test/src/common/harnessFork.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/common/harnessFork.h)
- [test/src/module/command/archiveGetTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/command/archiveGetTest.c)

# Making a change

[Wiki home](../README.md)

Choose the owning subsystem, then follow shared contracts. Cross-cutting failures often come from missing one consumer of configuration, metadata, or protocol data.

| Change | Inspect together |
|---|---|
| New option | YAML, help XML, generation, parse/load tests |
| File transformation | Filter, packed reconstruction, empty/flush/buffer tests |
| Worker parameter | Coordinator writer, handler reader, file API, worker tests |
| Manifest field | Parse/save/defaults, validation, restore/verify/render |
| Storage behavior | Capabilities, common API, backend, backend tests |
| GPDB recovery | Fork/version guards, relation selection, record processors, integration |

A temporary allocation or cached object must not outlive its owner. Preserve secure-parameter logging conventions. Treat persistent formats and pack schemas as interfaces even when all participants live in one repository.

## Validation and handoff

Start with focused functional tests. Add the relevant coverage/Valgrind checks, generation and format validation, and version-dependent build/test environments. Local smoke evidence has a useful but limited scope; record the exact checks and untested platforms.

`CODING.md` specifies four-space indentation and a 132-character code/test line limit. The formatter enforces only part of the conventions. `CONTRIBUTING.md` documents testing, generation, and contribution procedures.

For a handoff, include branch/commit, intended behavior, affected contracts, test commands/results, and unresolved checks. Link to the relevant wiki pages instead of collecting every detail in one overview. Update the subsystem pages when behavior changes.

See [build](build.md), [testing](testing.md), [core runtime](../architecture/core-runtime.md), and [wiki maintenance](wiki-maintenance.md).

## Source entry points

- [CODING.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/CODING.md)
- [CONTRIBUTING.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/CONTRIBUTING.md)
- [test/define.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/define.yaml)
- [src/command/restore/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/protocol.c)

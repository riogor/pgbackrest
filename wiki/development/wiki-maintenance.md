# Maintaining and using this wiki

[Wiki home](../README.md)

The wiki is a source-backed navigation aid for contributors and assistants. It does not override actual code, repository instructions, or the user's task.

## Efficient reading

Begin with the index/source map. Read the workflow for the operation, then only the architecture/format pages it references. For a failing test, start with Testing and the closest example. Environment notes matter when executing in the prepared workspace.

## Updating pages

1. Record a new baseline commit/date when refreshing architecture.
2. Read changed source and tests before revising behavioral claims.
3. Put lasting design in subsystem pages, branch history in fork notes, and machine-specific observations in environment notes.
4. Preserve relative wiki links and add pinned source references.
5. Mark illustrative examples and identify operational assumptions.
6. Distinguish inspected code, successful test results, and checks not performed.

Pinned source links stay historically accurate but can become stale relative to a new branch. The index identifies the baseline; the test-fix notes explicitly link their later commit.

## Scope

These notes do not certify recoverability or replace a restore exercise. They do not enumerate every option or backend endpoint. Source, built-in help, command/config references, and repository contribution rules provide the detailed contracts.

This wiki is published as files under `wiki/` on a separate documentation branch of the user's fork. It can be browsed on GitHub or cloned. Publishing it does not change PR #112 or its source branch. Recheck temporary paths and service state after a cloud environment change; uncommitted cloud files are not a durable delivery mechanism.

Refresh when command/option families, protocol/persistent formats, supported versions, CI images, or the prepared workspace change.

## Source entry points

- [CONTRIBUTING.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/CONTRIBUTING.md)
- [src/version.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/version.h)

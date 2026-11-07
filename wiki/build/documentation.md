# Documentation sources and executable examples

[Wiki home](../README.md)

Scope: the project's existing documentation system, separate from this Markdown wiki. [Build overview](../development/build.md).

`doc/doc.pl` orchestrates documentation builds. XML sources and Perl support describe pages/examples/environments; C tools under `doc/src/` render reference/man-related outputs using project configuration/help data.

## Executed examples

Documentation generation can execute statements in prepared/container environments and cache their results. Text-only proofing can reuse cached outputs; a no-cache build forces examples to run again. Changing an example's behavior therefore needs different validation from changing prose alone.

OS/image variables choose example environments. Package-based builds can exercise a supplied package rather than the source-built binary, providing broad smoke coverage. HTML and PDF output have different tool dependencies; consult `doc/README.md` before installing a large document toolchain.

## Relationships

Option descriptions in `src/build/help/help.xml` feed compiled help and reference generation. Command/config schema edits can affect both executable help and rendered docs. Do not update a wiki claim while leaving an authoritative option description wrong.

The main CI matrix has documentation jobs because examples exercise real backup/restore/configuration behavior. A documentation failure may be an operational/environment problem rather than XML syntax.

See [generators](generators.md), [CI](../development/ci.md), and [wiki maintenance](../development/wiki-maintenance.md) for the distinct update roles.

## Source entry points

- [doc/README.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/doc/README.md)
- [doc/doc.pl](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/doc/doc.pl)
- [doc/src/command/build/reference.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/doc/src/command/build/reference.c)
- [doc/src/command/build/man.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/doc/src/command/build/man.c)
- [src/build/help/help.xml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/help/help.xml)

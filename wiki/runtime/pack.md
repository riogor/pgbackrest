# Pack encoding and field contracts

[Wiki home](../README.md)

Scope: compact typed serialization used by protocols, filter parameters, and results. [Protocol overview](../architecture/protocol.md).

`Pack` encodes typed fields identified by increasing numeric IDs. Integer encoding uses base-128 varints and is independent of sender/receiver host endianness. It is a sequential format; arbitrary reads into its middle are generally impractical.

## Defaults are part of the contract

A writer normally omits a value equal to its default while advancing the field ID. Missing/null fields are represented by gaps, not independently typed null values. If a writer uses a custom default, the reader must apply the same default.

`.defaultWrite` can force writing a supported default value. Explicit IDs permit skipping fields, but must be unique/increasing. Containers reset inner numbering and restore the enclosing numbering on completion.

## In command code

Coordinators commonly write fields in order with `pckWrite*P()`. Handlers read with matching `pckRead*P()` calls and complete the container. A field inserted on one side shifts the implicit field sequence on the other. Inspect types as well as order: an unsigned cipher identifier must not be read as a string.

Filter constructors can pack parameters for remote reconstruction; filter result packs communicate hashes, sizes, and detailed validation results.

Test empty/missing/default cases and the actual protocol route when changing a schema. A direct file-function test will not reveal a coordinator/handler mismatch.

See [protocol sessions](../protocol/sessions.md), [I/O filters](../architecture/io-filters.md), and [worker test shims](../testing/protocol-shims.md).

## Source entry points

- [src/common/type/pack.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/pack.h)
- [src/common/type/pack.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/pack.c)
- [src/command/restore/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/protocol.c)
- [src/command/verify/protocol.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/verify/protocol.c)

# Command and option schema rules

[Wiki home](../README.md)

Scope: what to change before regenerating option tables. [Configuration overview](../architecture/configuration.md).

`src/build/config/config.yaml` defines commands, option groups, and options. Command declarations describe allowed roles, required/local/remote locking, log defaults, and whether positional parameters are allowed.

## Option rules

Definitions describe type/default, allowed values/ranges, command applicability, groups, dependencies, secure handling, and deprecated names. Grouped user keys such as `pg1`/`repo2` are resolved into internal indexed values; they are not a fixed set of independent handwritten options.

A dependency may determine when an option is valid or required. New allowed values can change the validity/requirement of related options, as cipher type affects passphrase requirements. Include parse/load interactions, not only enum generation.

The parser supports negation/reset where applicable, source precedence, and command/role validation. A value valid for one command can still be invalid for another role. Preserving source metadata matters when effective options must propagate to workers/remotes.

## Change route

Edit the schema and explanatory help, regenerate, inspect derived changes, and add targeted parse/load tests. Do not add only a constant to `config.auto.h`. A secure flag also affects command logging and how tests should provide values.

See [generators](../build/generators.md), [libpq/config test patterns](../development/writing-tests.md), and [locks](../runtime/locks-stop-state.md).

## Source entry points

- [src/build/config/config.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/config/config.yaml)
- [src/build/config/parse.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/config/parse.c)
- [src/build/config/render.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/config/render.c)
- [src/config/parse.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/parse.c)
- [src/config/load.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/load.c)

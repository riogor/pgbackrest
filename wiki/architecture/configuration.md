# Configuration and generated options

[Wiki home](../README.md)

Configuration is a typed API backed by generated command/option rules. `src/build/config/config.yaml` is the authoritative definition; `src/build/help/help.xml` provides explanatory help text.

## Resolution

`cfgParse()` handles CLI arguments, environment variables, configuration files, defaults, and dependencies. CLI values take priority over environment values, which take priority over file values. File sections are searched from specific to general:

```text
[stanza:command] → [stanza] → [global:command] → [global]
```

Help/version skip normal environment/file loading so broken operational configuration does not prevent basic help. `cfgLoad()` handles broader startup; `cfgLoadUpdateOption()` adjusts related options and compatibility behavior, including GOST aliases on this branch.

## API patterns

| Form | Meaning |
|---|---|
| `cfgOptionStr()`, `cfgOptionBool()` | Typed access to the selected/default option |
| `cfgOptionIdxStr(..., repoIdx)` | Access one internal group index |
| `cfgOptionValid()` | Valid for this command/role? |
| `cfgOptionTest()` | Has a value? |
| `cfgOptionIdxSource()` | Source of the resolved value |

User-facing keys such as `repo1` are distinct from zero-based internal indexes. Key 1 commonly maps to index 0; sparse groups require particular care. Test helpers with `Key` in their name address user-facing keys.

Secure options are redacted in command logging. Tests commonly set repository passphrases through environment helpers. Keep the value's source intact when compatibility normalization must still propagate it to child/remote processes.

To add an option, edit YAML/help, regenerate derived definitions, and test parsing and load-time interactions. See [build](../development/build.md) and [writing tests](../development/writing-tests.md).

## Focused guides

- [Schema rules](../configuration/schema.md)
- [Generator pipeline](../build/generators.md)


## Source entry points

- [src/build/config/config.yaml](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/config/config.yaml)
- [src/config/parse.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/parse.c)
- [src/config/load.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/load.c)
- [src/config/config.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/config/config.h)

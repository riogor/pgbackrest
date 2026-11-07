# Code-generator inputs and renderers

[Wiki home](../README.md)

Scope: generated C definitions and why edits belong in their inputs. [Build overview](../development/build.md).

`src/build/main.c` dispatches generator modes to parse/render pairs. Config and error definitions use YAML; help uses XML plus config context; PostgreSQL generation uses its version definitions and vendor/interface inputs.

| Mode/family | Source responsibility |
|---|---|
| Config | Parse option rules and render identifiers/parse data |
| Error | Render typed error tables/declarations |
| Help | Combine option definitions and help text into compiled data |
| PostgreSQL | Render version-specific physical interfaces/version data |

`src/build/common/` provides YAML/XML reading and rendering utilities. Build output can target a build directory rather than modify every source artifact in place; inspect the invoking Make/Meson/test command when locating a generated file.

## Validation

The test runner provides generation-only and generation-check modes. CI uses generation checks to detect committed inputs/outputs out of sync. Ordinary compilation may generate only the artifacts it needs, so a successful binary build is not necessarily a full generated-file consistency check.

When a generated diff is unexpectedly large, check generator version/source and input normalization before editing output by hand. Verify both Make and Meson source/target relationships when a new generator input is introduced.

See [schema](../configuration/schema.md), [documentation](documentation.md), and [build route](../development/build.md).

## Source entry points

- [src/build/main.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/main.c)
- [src/build/common/render.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/common/render.c)
- [src/build/config/render.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/config/render.c)
- [src/build/help/render.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/help/render.c)
- [src/meson.build](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/meson.build)
- [src/Makefile.in](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/Makefile.in)

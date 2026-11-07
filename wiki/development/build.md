# Building and generated code

[Wiki home](../README.md)

Runtime code is primarily C99. The project supports Meson/Ninja and generated Autoconf configuration plus Make. Keep separate build directories so those routes do not share incompatible outputs.

## Build routes

With development libraries and the intended `pg_config`/libpq available, from the repository root:

```bash
meson setup ../pgbackrest-build-dev --buildtype=debug
ninja -C ../pgbackrest-build-dev
../pgbackrest-build-dev/src/pgbackrest version
```

Choose a fresh build directory. The documented Make debug route, from `src/`, is:

```bash
CFLAGS='-O0 -g3' ./configure --disable-optimize --enable-test
make -j4
./pgbackrest version
```

Meson requires OpenSSL >=1.1.1, libpq, libxml2, build-time libyaml, zlib, bzip2, and lz4. libssh2 and zstd are feature-controlled. Python participates in Meson setup; Perl supports testing/documentation. GOST engines/providers are separate from accepting cipher config values.

## Generation

| Authoritative input | Derived output/behavior |
|---|---|
| `src/build/config/config.yaml` | Option identifiers and parse-rule tables |
| `src/build/help/help.xml` | Compiled help and reference inputs |
| `src/build/error/error.yaml` | Error declarations/tables |
| `src/build/postgres/postgres.yaml` | Version-specific physical interfaces |
| `src/build/configure.ac` | Configure script |

`src/build/main.c` is the `build-code` entry point. Test tooling supports `--gen-only` and `--gen-check`; ordinary builds also generate required artifacts. Some outputs are tracked and others build-local. Editing only a generated table is not a complete option change.

CI uses a Meson unity build to expose visibility/symbol problems. Keep declarations and source lists compatible with both build routes. See [local environment](../environment/local.md) and [testing](testing.md).

## Source entry points

- [meson.build](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/meson.build)
- [src/meson.build](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/meson.build)
- [src/Makefile.in](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/Makefile.in)
- [src/build/main.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/build/main.c)
- [CONTRIBUTING.md](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/CONTRIBUTING.md)

# Detailed subsystem guides

[Wiki home](README.md)

Use this map after the [system overview](overview.md) and [source map](source-map.md). Each article owns one concrete contract or execution boundary. Overview pages remain the place to understand how these pieces connect.

## Runtime foundations

- [Memory contexts and callbacks](runtime/memory-contexts.md): ownership transfer, reset, cleanup.
- [Errors, retry history, and waits](runtime/errors-retry.md): error preservation versus retry scheduling.
- [Locks and stop state](runtime/locks-stop-state.md): process locks, progress, stanza markers.
- [Logging and statistics](runtime/logging-statistics.md): tracing, redaction, counters, log assertions.
- [Buffers, strings, identifiers](runtime/buffers-strings.md): used length and compact names.
- [Pack encoding](runtime/pack.md): field IDs, defaults, typed serialization.

## Network and worker services

- [HTTP requests](network/http.md): response handling, reuse, retries.
- [TLS services](network/tls-services.md): verification and stanza authorization.
- [Protocol sessions](protocol/sessions.md): handlers, session state, typed remote errors.

## Storage backends

- [POSIX and CIFS](storage/posix.md): write close, rename, sync, capabilities.
- [S3](storage/s3.md): signing, credentials, multipart completion.
- [Azure](storage/azure.md): authentication and block-list commit.
- [GCS](storage/gcs.md): tokens, resumable uploads, incomplete responses.
- [SFTP](storage/sftp.md): libssh2, host keys, user authentication.
- [Remote storage](storage/remote.md): streaming sessions and remote filters.

## WAL archiving

- [WAL identity](archive/wal-identity.md): segment names, archive IDs, history files.
- [Spool status](archive/spool-status.md): acknowledgements, errors, stale state.
- [Async push](archive/async-push.md): ready WAL, workers, repeat transfers.
- [Async get](archive/async-get.md): prefetch, queue planning, missing WAL.

## Backup and restore details

- [Backup file pipeline](backup/file-pipeline.md): filters, results, stored/logical measurements.
- [Ancestry and resume](backup/ancestry-resume.md): dependencies and resume eligibility.
- [Standby backup](backup/standby.md): replay coordination and source selection.
- [Restore preparation](restore/target-preparation.md): existing files, mappings, cleanup guards.
- [Recovery settings](restore/recovery-settings.md): version-specific files and targets.

## Metadata and integrity

- [Info loading](metadata/info-loading.md): primary/copy fallback and error context.
- [Database history](metadata/database-history.md): system identity and upgrades.
- [Checksum purposes](integrity/checksums.md): logical/stored/page/block validation.
- [Verification](integrity/verification.md): planning, worker checks, remote sink.
- [Retention](integrity/retention.md): dependency-aware expiration and WAL ranges.
- [Cipher lifecycle](crypto/cipher-lifecycle.md): fetched/borrowed ownership and finalization.
- [Compression](filters/compression.md): codecs, filenames, stream boundaries.

## Configuration, generation, and testing

- [Command/option schema](configuration/schema.md): roles, dependencies, secure/grouped options.
- [Generators](build/generators.md): authoritative inputs, outputs, consistency.
- [Documentation builds](build/documentation.md): executable examples and caching.
- [Protocol shims](testing/protocol-shims.md): real handlers in controlled children.
- [libpq scripts](testing/pq-scripts.md): exact SQL/client sequences without a server.
- [Coverage and memory checks](testing/coverage.md): different validation layers.
- [Storage fixtures](testing/storage-fixtures.md): files, metadata, physical headers.

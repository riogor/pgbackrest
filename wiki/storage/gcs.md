# GCS tokens and resumable uploads

[Wiki home](../README.md)

Scope: Google Cloud Storage backend mechanics. [Storage overview](../architecture/storage.md).

`storage/gcs/storage.c` owns authentication/token handling, requests, listing, and response interpretation. Parsed tokens carry expiration state; supported service/automatic authentication paths should not be treated as a static string copied forever.

## Upload lifecycle

The writer maintains buffered data, upload-session state, and outstanding request state. Resumable upload responses can report an incomplete upload before final completion. Backend response handling explicitly distinguishes allowed incomplete responses from an error.

When changing the writer, preserve content-range/offset state and the distinction between a nonfinal chunk and the final request. A successful intermediate request does not establish that the object has reached its final size.

JSON/token service errors and object-operation errors arise at separate stages. The shared HTTP transport handles connection/request mechanics; the backend decides how those API responses affect upload state.

Object names/version identifiers are not filesystem inode/path semantics. Follow capability and listing/version logic when adding target-time or removal behavior.

Repository-level compression/encryption is applied above the service API. `storage/gcsTest.c` checks service-specific behavior; read it before extending token types or chunk handling.

See [HTTP](../network/http.md), [S3](s3.md), and [Azure](azure.md) for related but different request lifecycles.

## Source entry points

- [src/storage/gcs/storage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/gcs/storage.c)
- [src/storage/gcs/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/gcs/write.c)
- [src/storage/gcs/read.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/gcs/read.c)
- [test/src/module/storage/gcsTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/storage/gcsTest.c)

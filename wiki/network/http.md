# HTTP requests, responses, and session reuse

[Wiki home](../README.md)

Scope: the common transport beneath object-store backends. [Storage overview](../architecture/storage.md).

`HttpClient` wraps an I/O client, commonly TLS over a socket, and opens reusable HTTP sessions. `HttpRequest` assembles verb/path/query/headers/content; `HttpResponse` parses status, headers, and body. HTTP utilities own encoding and header/query details so backends can focus on service semantics.

Requests may be started asynchronously and completed when their response is needed. Backend multipart writers use this to overlap buffered work with one outstanding request; this is not a guarantee of unlimited request concurrency.

## Retries and resources

Request code integrates `Wait` and `ErrorRetry`. Inspect its response/error classification before assuming every non-success response is retried. The storage backend still determines what a service response means, including allowed missing/incomplete outcomes.

A usable reusable session and a parsed response are separate resources. Response-body consumption and errors affect whether the session can be returned for reuse. Do not retain objects from a request's temporary context after it is freed.

Session/request/retry counters help distinguish repeated requests from repeated connections. TLS setup has its own timeout/retry state.

For a failed object upload, trace backend request creation → HTTP response handling → TLS/socket failure. Authentication/signing logic belongs to each backend; repository encryption belongs above this transport.

See [TLS services](tls-services.md), [S3](../storage/s3.md), [Azure](../storage/azure.md), [GCS](../storage/gcs.md), and [retry reporting](../runtime/errors-retry.md).

## Source entry points

- [src/common/io/http/client.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/http/client.h)
- [src/common/io/http/request.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/http/request.c)
- [src/common/io/http/response.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/http/response.c)
- [src/common/io/http/session.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/io/http/session.c)

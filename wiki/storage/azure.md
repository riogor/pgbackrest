# Azure Blob requests and block commits

[Wiki home](../README.md)

Scope: Azure repository storage. [Storage overview](../architecture/storage.md).

The Azure backend separates common HTTP/auth/listing behavior in `storage.c` from read and write objects. Shared-key authentication signs the appropriate request information; SAS authentication merges configured SAS query data. Both require correctly formed host/service requests.

## Writer lifecycle

The writer buffers content into blocks, assigns block identifiers, submits block requests, and records their order. Before submitting another asynchronous block it checks the previous request's response. Larger writes require final block-list commit behavior; small-object handling has its own completion path.

A block being present server-side is therefore a different state from a completed committed blob. Diagnose block submission and final commit independently. Preserve block-ID ordering and request ownership when changing buffering.

Azure container/blob paths map to storage names, not real POSIX directories. Path creation assumptions must remain behind storage capability checks.

Repository encryption transforms bytes before the backend; Azure authentication and any service features do not choose the manifest's cipher type. Keep those configuration domains separate in both code and diagnostics.

`storage/azureTest.c` exercises API requests/responses. See [HTTP transport](../network/http.md), [S3 multipart writes](s3.md), and [GCS resumable uploads](gcs.md).

## Source entry points

- [src/storage/azure/storage.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/azure/storage.c)
- [src/storage/azure/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/azure/write.c)
- [src/storage/azure/read.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/azure/read.c)
- [test/src/module/storage/azureTest.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/test/src/module/storage/azureTest.c)

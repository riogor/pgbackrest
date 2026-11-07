# Bundles and block incremental data

[Wiki home](../README.md)

Bundling combines multiple file payloads into larger repository objects. Block incremental encoding stores changed portions of a changed file. These are distinct features, and their location metadata can be combined.

## Representation

`BlockIncr` compares a file against a prior block map and emits changed content in super blocks followed by a block map. A super block groups multiple blocks for compression efficiency, at the cost of sequentially reading within that group.

`BlockMapItem` records backup reference, bundle identity, super-block offset/size, block position, and checksum. The map points to content; it does not contain the changed data itself. The filter returns map size so readers can locate the map at the end of the encoded region.

The map is not compressed and is separately encrypted when required. Writers/readers must agree on cipher type and raw framing, not only passphrase. Compression/encryption can change stored sizes, so logical offsets cannot be substituted for repository offsets.

xxHash detects changed blocks. Whole-file SHA-1 validates the reconstructed logical file. These checksum purposes are separate: change detection is not the final correctness check.

## Restore

`restore/blockDelta.c` plans reads for referenced super blocks and applies blocks to reconstruct output. Restore workers combine this with manifest bundle offsets and checksums. The manifest renderer can inspect related data.

When debugging, identify whether a size is logical file size, stored size, super-block size, or map size. Inspect references across backups as well as the current backup object.

See [manifests](manifests.md), [streaming filters](../architecture/io-filters.md), and [restore](../workflows/restore.md).

## Focused guides

- [Checksum purposes](../integrity/checksums.md)
- [Compression](../filters/compression.md)


## Source entry points

- [src/command/backup/blockIncr.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/blockIncr.h)
- [src/command/backup/blockMap.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/backup/blockMap.h)
- [src/command/restore/blockDelta.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/blockDelta.c)
- [src/command/restore/file.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/restore/file.c)

# Memory contexts and resource callbacks

[Wiki home](../README.md)

Scope: allocation lifetime and non-memory resource cleanup. [Core runtime overview](../architecture/core-runtime.md).

Every allocation belongs to a memory context. Contexts form a parent/child tree; freeing a parent releases its descendants and allocations. Objects conventionally own a context, so object destruction also destroys dependent state.

## Construction and transfer

`OBJ_NEW_BEGIN/END` establishes an object's construction scope. `MEM_CONTEXT_TEMP_BEGIN/END` bounds scratch work. Allocate long-lived fields in the object's context rather than whichever temporary context happens to be active.

Returning a pointer from a temporary scope requires transferring its ownership, commonly with a type-specific `*Move()` and `memContextPrior()`. Moving the container does not make unrelated borrowed pointers independent allocations.

Resettable temporary contexts support long processing loops without retaining every iteration's allocations. They also make references into prior iterations invalid. When reviewing a pointer, identify its owner and all operations that can free/reset that owner.

## External resources

Context callbacks release handles such as file descriptors, cipher contexts, and fetched OpenSSL objects. A normal close path must coordinate with the callback so resources are not closed twice. POSIX writes clear their registered close callback before explicit close; cipher objects distinguish fetched owned objects from borrowed lookup results.

Cleanup also participates in error unwinding. An error path is not a reason to assume ordinary function epilogues run. Test failures during construction as well as after successful construction.

See [cipher lifetime](../crypto/cipher-lifecycle.md), [POSIX writes](../storage/posix.md), and [errors/retries](errors-retry.md).

## Source entry points

- [src/common/memContext.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/memContext.h)
- [src/common/memContext.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/memContext.c)
- [src/common/type/object.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/type/object.c)
- [src/storage/posix/write.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/storage/posix/write.c)

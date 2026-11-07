# Command locks and stanza stop state

[Wiki home](../README.md)

Scope: concurrency control and intentional stop markers. [Runtime overview](../architecture/runtime.md).

The common lock layer acquires/releases locks and writes progress data. `LockData` contains process ID, execution ID, and optional completed/total sizes and percentage. A lock filename's existence alone is not proof a live operation holds it: `lockRead()` distinguishes missing, unlocked, invalid, and valid states.

`command/lock.c` maps configured command lock types onto concrete lock acquisition. Command definitions declare whether a lock is required, its type, and whether the remote needs it. Remote lock rules apply to the appropriate process role rather than indiscriminately to every worker.

## Stop is a separate mechanism

`lockStopFileName()` chooses a stanza marker or the all-stanzas marker under `lock-path`. `lockStopTest()` checks the selected stanza and the global marker and raises `StopError` when appropriate. Start/stop commands manage this state.

Releasing a lock does not mean a stanza was started; deleting a stop marker does not mean a previous process ended. Keep these concepts distinct when diagnosing a refused operation.

## Tests

Concurrency tests use fork synchronization so a child holds a lock before the parent attempts the operation. This is more deterministic than relying on a delay. Check nonthrowing `returnOnNoLock` behavior as well as default errors and progress-data parsing.

See [protocol shims](../testing/protocol-shims.md) and [command schema](../configuration/schema.md) for role/lock declaration details.

## Source entry points

- [src/common/lock.h](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/lock.h)
- [src/common/lock.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/common/lock.c)
- [src/command/lock.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/lock.c)
- [src/command/control/common.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/control/common.c)
- [src/command/control/start.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/control/start.c)
- [src/command/control/stop.c](https://github.com/riogor/pgbackrest/blob/b8ce272231bac178ba39fffc8561b058ab150a0b/src/command/control/stop.c)

# BSD

FreeBSD, OpenBSD, NetBSD, and DragonFly BSD x86_64 syscall bindings for [Rux](https://rux-lang.dev).

## Installation

You can install the package using Rux CLI

```sh
rux add BSD
```

Add `BSD` to your `Rux.toml`:

```toml
[Dependencies]
BSD = "*"
```

## Overview

This package exposes a small BSD syscall foundation. It does not depend on
libc; calls are backed by Rux compiler support for
`__rux_linux_syscall0` through `__rux_linux_syscall6` and per-BSD thunks
for `mmap`, `nanosleep`, and `clock_gettime`.

### Raw syscall wrappers

| Function | Description |
| --- | --- |
| `Syscall0` ... `Syscall6` | Invoke a BSD x86_64 syscall with 0 to 6 arguments |
| `IsError(result)` | Returns `true` for BSD positive errno results |
| `Errno(result)` | Converts a positive errno result to a positive errno value |

### Basic helpers

| Function | Description |
| --- | --- |
| `Read(fd, buffer, count)` | Call `read(2)` |
| `Write(fd, buffer, count)` | Call `write(2)` |
| `Close(fd)` | Call `close(2)` |
| `Exit(code)` | Call `exit(2)` |
| `GetPid()` | Call `getpid(2)` |
| `Mmap(addr, length, prot, flags, fd, offset)` | Call `mmap(2)` |
| `Munmap(addr, length)` | Call `munmap(2)` |
| `Brk(addr)` | Call `brk(2)` |
| `Nanosleep(req, rem)` | Call `nanosleep(2)` |
| `ClockGettime(clockId, ts)` | Call `clock_gettime(2)` |

All syscall helpers return the raw BSD result as `int64`: non-negative values
are successful results, while values from `1` through `4095` represent positive
errno (BSD convention). This differs from Linux, which uses negative errno.

## Constants

- File descriptors: `Stdin`, `Stdout`, `Stderr`
- Syscall numbers: `SYS_READ`, `SYS_WRITE`, `SYS_CLOSE`, `SYS_EXIT`,
  `SYS_GETPID`, `SYS_BRK`, `SYS_MUNMAP`
- Memory flags: `PROT_READ`, `PROT_WRITE`, `PROT_EXEC`, `MAP_PRIVATE`,
  `MAP_ANONYMOUS`
- Clock IDs: `CLOCK_MONOTONIC`, `CLOCK_REALTIME`

## Types

| Type | Description |
| --- | --- |
| `timespec` | Struct with `tv_sec: int64` and `tv_nsec: int64` |

## Example

```rux
import BSD::{ Write, Stdout };

func Main() -> int {
    let msg = "hello from BSD\n";
    let result = Write(Stdout, msg.data, msg.length);
    return result == msg.length as int64 ? 0 : 1;
}
```

## Requirements

- FreeBSD, OpenBSD, NetBSD, or DragonFly BSD x86_64
- A Rux compiler with BSD syscall thunk support

## License

[MIT](LICENSE)

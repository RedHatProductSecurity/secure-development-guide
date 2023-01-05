- [File Descriptor Management](#file-descriptor-management)
  - [Closing Descriptors](#closing-descriptors)
    - [Error Handling during Descriptor Close](#error-handling-during-descriptor-close)
    - [Closing Descriptors and Race Conditions](#closing-descriptors-and-race-conditions)
    - [Lingering State after Close](#lingering-state-after-close)
  - [Preventing File Descriptor Leaks to Child Processes](#preventing-file-descriptor-leaks-to-child-processes)
  - [Dealing with the `select` Limit](#dealing-with-the-select-limit)

# File Descriptor Management

File descriptors underlie all input/output mechanisms offered by the
system. They are used to implementation the `FILE
*`-based functions found in `<stdio.h>`, and all the file and network
communication facilities provided by the Python and Java environments
are eventually implemented in them.

File descriptors are small, non-negative integers in userspace, and are
backed on the kernel side with complicated data structures which can
sometimes grow very large.

## Closing Descriptors

If a descriptor is no longer used by a program and is not closed
explicitly, its number cannot be reused (which is problematic in itself,
see [Dealing with the
Limit](#sect-Defensive_Coding-Tasks-Descriptors-Limit)), and the kernel
resources are not freed. Therefore, it is important to close all
descriptors at the earliest point in time possible, but not earlier.

### Error Handling during Descriptor Close

The `close` system call is always successful in the sense that the
passed file descriptor is never valid after the function has been
called. However, `close` still can return an error, for example if there
was a file system failure. But this error is not very useful because the
absence of an error does not mean that all caches have been emptied and
previous writes have been made durable. Programs which need such
guarantees must open files with `O_SYNC` or use `fsync` or `fdatasync`,
and may also have to `fsync` the directory containing the file.

### Closing Descriptors and Race Conditions

Unlike process IDs, which are recycle only gradually, the kernel always
allocates the lowest unused file descriptor when a new descriptor is
created. This means that in a multi-threaded program which constantly
opens and closes file descriptors, descriptors are reused very quickly.
Unless descriptor closing and other operations on the same file
descriptor are synchronized (typically, using a mutex), there will be
race conditions and I/O operations will be applied to the wrong file
descriptor.

Sometimes, it is necessary to close a file descriptor concurrently,
while another thread might be about to use it in a system call. In order
to support this, a program needs to create a single special file
descriptor, one on which all I/O operations fail. One way to achieve
this is to use `socketpair`, close one of the descriptors, and call
`shutdown(fd, SHUTRDWR)` on the other.

When a descriptor is closed concurrently, the program does not call
`close` on the descriptor. Instead it program uses `dup2` to replace the
descriptor to be closed with the dummy descriptor created earlier. This
way, the kernel will not reuse the descriptor, but it will carry out all
other steps associated with calling a descriptor (for instance, if the
descriptor refers to a stream socket, the peer will be notified).

This is just a sketch, and many details are missing. Additional data
structures are needed to determine when it is safe to really close the
descriptor, and proper locking is required for that.

### Lingering State after Close

By default, closing a stream socket returns immediately, and the kernel
will try to send the data in the background. This means that it is
impossible to implement accurate accounting of network-related resource
utilization from userspace.

The `SO_LINGER` socket option alters the behavior of `close`, so that it
will return only after the lingering data has been processed, either by
sending it to the peer successfully, or by discarding it after the
configured timeout. However, there is no interface which could perform
this operation in the background, so a separate userspace thread is
needed for each `close` call, causing scalability issues.

Currently, there is no application-level countermeasure which applies
universally. Mitigation is possible with **<span
class="application">iptables</span>** (the `connlimit` match type in
particular) and specialized filtering devices for denial-of-service
network traffic.

These problems are not related to the `TIME_WAIT` state commonly seen in
**<span class="application">netstat</span>** output. The kernel
automatically expires such sockets if necessary.

## Preventing File Descriptor Leaks to Child Processes

Child processes created with `fork` share the initial set of file
descriptors with their parent process. By default, file descriptors are
also preserved if a new process image is created with `execve` (or any
of the other functions such as `system` or `posix_spawn`).

Usually, this behavior is not desirable. There are two ways to turn it
off, that is, to prevent new process images from inheriting the file
descriptors in the parent process:

-   Set the close-on-exec flag on all newly created file descriptors.
    Traditionally, this flag is controlled by the `FD_CLOEXEC` flag,
    using `F_GETFD` and `F_SETFD` operations of the `fcntl` function.

    However, in a multi-threaded process, there is a race condition: a
    subprocess could have been created between the time the descriptor
    was created and the `FD_CLOEXEC` was set. Therefore, many system
    calls which create descriptors (such as `open` and `openat`) now
    accept the `O_CLOEXEC` flag (`SOCK_CLOEXEC` for `socket` and
    `socketpair`), which cause the `FD_CLOEXEC` flag to be set for the
    file descriptor in an atomic fashion. In addition, a few new systems
    calls were introduced, such as `pipe2` and `dup3`.

    The downside of this approach is that every descriptor needs to
    receive special treatment at the time of creation, otherwise it is
    not completely effective.

-   After calling `fork`, but before creating a new process image with
    `execve`, all file descriptors which the child process will not need
    are closed.

    Traditionally, this was implemented as a loop over file descriptors
    ranging from `3` to `255` and later `1023`. But this is only an
    approximation because it is possible to create file descriptors
    outside this range easily (see [Dealing with the
    Limit](#sect-Defensive_Coding-Tasks-Descriptors-Limit)). Another
    approach reads `/proc/self/fd` and closes the unexpected descriptors
    listed there, but this approach is much slower.

At present, environments which care about file descriptor leakage
implement the second approach. OpenJDK 6 and 7 are among them.

## Dealing with the `select` Limit

By default, a user is allowed to open only 1024 files in a single
process, but the system administrator can easily change this limit
(which is necessary for busy network servers). However, there is another
restriction which is more difficult to overcome.

The `select` function only supports a maximum of `FD_SETSIZE` file
descriptors (that is, the maximum permitted value for a file descriptor
is `FD_SETSIZE - 1`, usually 1023.) If a process opens many files,
descriptors may exceed such limits. It is impossible to query such
descriptors using `select`.

If a library which creates many file descriptors is used in the same
process as a library which uses `select`, at least one of them needs to
be changed. Calls to `select` can be replaced with calls to `poll` or
another event handling mechanism. Replacing the `select` function is the
recommended approach.

Alternatively, the library with high descriptor usage can relocate
descriptors above the `FD_SETSIZE` limit using the following procedure.

-   Create the file descriptor `fd` as usual, preferably with the
    `O_CLOEXEC` flag.

-   Before doing anything else with the descriptor `fd`, invoke:

<!-- -->

          int newfd = fcntl(fd, F_DUPFD_CLOEXEC, (long)FD_SETSIZE);

-   Check that `newfd` result is non-negative, otherwise close `fd` and
    report an error, and return.

-   Close `fd` and continue to use `newfd`.

The new descriptor has been allocated above the `FD_SETSIZE`. Even
though this algorithm is racy in the sense that the `FD_SETSIZE` first
descriptors could fill up, a very high degree of physical parallelism is
required before this becomes a problem.

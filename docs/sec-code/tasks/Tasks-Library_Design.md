Unresolved directive in Tasks-Library_Design.adoc -
include::partial$entities.adoc\[\]

# Library Design

Through this section, the term **client code** refers to applications
and other libraries using the library.

## State Management

### Global State

Global state should be avoided.

If this is impossible, the global state must be protected with a lock.
For C/C++, you can use the `pthread_mutex_lock` and
`pthread_mutex_unlock` functions without linking against `-lpthread`
because the system provides stubs for non-threaded processes.

For compatibility with `fork`, these locks should be acquired and
released in helpers registered with `pthread_atfork`. This function is
not available without `-lpthread`, so you need to use `dlsym` or a weak
symbol to obtain its address.

If you need `fork` protection for other reasons, you should store the
process ID and compare it to the value returned by `getpid` each time
you access the global state. (`getpid` is not implemented as a system
call and is fast.) If the value changes, you know that you have to
re-create the state object. (This needs to be combined with locking, of
course.)

### Handles

Library state should be kept behind a curtain. Client code should
receive only a handle. In C, the handle can be a pointer to an
incomplete `struct`. In C++, the handle can be a pointer to an abstract
base class, or it can be hidden using the pointer-to-implementation
idiom.

The library should provide functions for creating and destroying
handles. (In C++, it is possible to use virtual destructors for the
latter.) Consistency between creation and destruction of handles is
strongly recommended: If the client code created a handle, it is the
responsibility of the client code to destroy it. (This is not always
possible or convenient, so sometimes, a transfer of ownership has to
happen.)

Using handles ensures that it is possible to change the way the library
represents state in a way that is transparent to client code. This is
important to facilitate security updates and many other code changes.

It is not always necessary to protect state behind a handle with a lock.
This depends on the level of thread safety the library provides.

## Object Orientation

Classes should be either designed as base classes, or it should be
impossible to use them as base classes (like `final` classes in Java).
Classes which are not designed for inheritance and are used as base
classes nevertheless create potential maintenance hazards because it is
difficult to predict how client code will react when calls to virtual
methods are added, reordered or removed.

Virtual member functions can be used as callbacks. See
[Callbacks](#sect-Defensive_Coding-Tasks-Library_Design-Callbacks) for
some of the challenges involved.

## Callbacks

Higher-order code is difficult to analyze for humans and computers
alike, so it should be avoided. Often, an iterator-based interface (a
library function which is called repeatedly by client code and returns a
stream of events) leads to a better design which is easier to document
and use.

If callbacks are unavoidable, some guidelines for them follow.

In modern C++ code, `std::function` objects should be used for
callbacks.

In older C++ code and in C code, all callbacks must have an additional
closure parameter of type `void *`, the value of which can be specified
by client code. If possible, the value of the closure parameter should
be provided by client code at the same time a specific callback is
registered (or specified as a function argument). If a single closure
parameter is shared by multiple callbacks, flexibility is greatly
reduced, and conflicts between different pieces of client code using the
same library object could be unresolvable. In some cases, it makes sense
to provide a de-registration callback which can be used to destroy the
closure parameter when the callback is no longer used.

Callbacks can throw exceptions or call `longjmp`. If possible, all
library objects should remain in a valid state. (All further operations
on them can fail, but it should be possible to deallocate them without
causing resource leaks.)

The presence of callbacks raises the question if functions provided by
the library are **reentrant**. Unless a library was designed for such
use, bad things will happen if a callback function uses functions in the
same library (particularly if they are invoked on the same objects and
manipulate the same state). When the callback is invoked, the library
can be in an inconsistent state. Reentrant functions are more difficult
to write than thread-safe functions (by definition, simple locking would
immediately lead to deadlocks). It is also difficult to decide what to
do when destruction of an object which is currently processing a
callback is requested.

## Process Attributes

Several attributes are global and affect all code in the process, not
just the library that manipulates them.

-   environment variables (see [Accessing Environment
    Variables](tasks/Tasks-Processes.xml#sect-Defensive_Coding-Tasks-secure_getenv))

-   umask

-   user IDs, group IDs and capabilities

-   current working directory

-   signal handlers, signal masks and signal delivery

-   file locks (especially `fcntl` locks behave in surprising ways, not
    just in a multi-threaded environment)

Library code should avoid manipulating these global process attributes.
It should not rely on environment variables, umask, the current working
directory and signal masks because these attributes can be inherited
from an untrusted source.

In addition, there are obvious process-wide aspects such as the virtual
memory layout, the set of open files and dynamic shared objects, but
with the exception of shared objects, these can be manipulated in a
relatively isolated way.

- [Temporary Files](#temporary-files)
  - [Obtaining the Location of Temporary Directory](#obtaining-the-location-of-temporary-directory)
  - [Named Temporary Files](#named-temporary-files)
  - [Temporary Files without Names](#temporary-files-without-names)
  - [Temporary Directories](#temporary-directories)
  - [Compensating for Unsafe File Creation](#compensating-for-unsafe-file-creation)

# Temporary Files

In this chapter, we describe how to create temporary files and
directories, how to remove them, and how to work with programs which do
not create files in ways that are safe with a shared directory for
temporary files. General file system manipulation is treated in a
separate chapter, [File System
Manipulation](Tasks-File_System).

Secure creation of temporary files has four different aspects.

-   The location of the directory for temporary files must be obtained
    in a secure manner (that is, untrusted environment variables must be
    ignored, see
    xref:tasks/Tasks-Processes#accessing-environment-variables).

-   A new file must be created. Reusing an existing file must be avoided
    (the `/tmp` race condition). This is tricky because traditionally,
    system-wide temporary directories shared by all users are used.

-   The file must be created in a way that makes it impossible for other
    users to open it.

-   The descriptor for the temporary file should not leak to
    subprocesses.

All functions mentioned below will take care of these aspects.

Traditionally, temporary files are often used to reduce memory usage of
programs. More and more systems use RAM-based file systems such as
`tmpfs` for storing temporary files, to increase performance and
decrease wear on Flash storage. As a result, spooling data to temporary
files does not result in any memory savings, and the related complexity
can be avoided if the data is kept in process memory.

## Obtaining the Location of Temporary Directory

Some functions below need the location of a directory which stores
temporary files. For C/C++ programs, use the following steps to obtain
that directory:

-   Use `secure_getenv` to obtain the value of the `TMPDIR` environment
    variable. If it is set, convert the path to a fully-resolved
    absolute path, using `realpath(path, NULL)`. Check if the new path
    refers to a directory and is writeable. In this case, use it as the
    temporary directory.

-   Fall back to `/tmp`.

In Python, you can use the `tempfile.tempdir` variable.

Java does not support SUID/SGID programs, so you can use the
`java.lang.System.getenv(String)` method to obtain the value of the
`TMPDIR` environment variable, and follow the two steps described above.
(Java’s default directory selection does not honor `TMPDIR`.)

## Named Temporary Files

The `mkostemp` function creates a named temporary file. You should
specify the `O_CLOEXEC` flag to avoid file descriptor leaks to
subprocesses. (Applications which do not use multiple threads can also
use `mkstemp`, but libraries should use `mkostemp`.) For determining the
directory part of the file name pattern, see [Obtaining the Location of
Temporary
Directory](#obtaining-the-location-of-temporary-directory)

The file is not removed automatically. It is not safe to rename or
delete the file before processing, or transform the name in any way (for
example, by adding a file extension). If you need multiple temporary
files, call `mkostemp` multiple times. Do not create additional file
names derived from the name provided by a previous `mkostemp` call.
However, it is safe to close the descriptor returned by `mkostemp` and
reopen the file using the generated name.

The Python class `tempfile.NamedTemporaryFile` provides similar
functionality, except that the file is deleted automatically by default.
Note that you may have to use the `file` attribute to obtain the actual
file object because some programming interfaces cannot deal with
file-like objects. The C function `mkostemp` is also available as
`tempfile.mkstemp`.

In Java, you can use the `java.io.File.createTempFile(String, String,
File)` function, using the temporary file location determined according
to [Obtaining the Location of Temporary
Directory](#obtaining-the-location-of-temporary-directory). Do
not use `java.io.File.deleteOnExit()` to delete temporary files, and do
not register a shutdown hook for each temporary file you create. In both
cases, the deletion hint cannot be removed from the system if you delete
the temporary file prior to termination of the VM, causing a memory
leak.

## Temporary Files without Names

The `tmpfile` function creates a temporary file and immediately deletes
it, while keeping the file open. As a result, the file lacks a name and
its space is deallocated as soon as the file descriptor is closed
(including the implicit close when the process terminates). This avoids
cluttering the temporary directory with orphaned files.

Alternatively, if the maximum size of the temporary file is known
beforehand, the `fmemopen` function can be used to create a `FILE *`
object which is backed by memory.

In Python, unnamed temporary files are provided by the
`tempfile.TemporaryFile` class, and the `tempfile.SpooledTemporaryFile`
class provides a way to avoid creation of small temporary files.

Java does not support unnamed temporary files.

## Temporary Directories

The `mkdtemp` function can be used to create a temporary directory. (For
determining the directory part of the file name pattern, see [Obtaining
the Location of Temporary
Directory](#obtaining-the-location-of-temporary-directory) The
directory is not automatically removed. In Python, this function is
available as `tempfile.mkdtemp`. In Java 7, temporary directories can be
created using the `java.nio.file.Files.createTempDirectory(Path, String,
FileAttribute…​)` function.

When creating files in the temporary directory, use automatically
generated names, e.g., derived from a sequential counter. Files with
externally provided names could be picked up in unexpected contexts, and
crafted names could actually point outside of the tempoary directory
(due to **directory traversal**).

Removing a directory tree in a completely safe manner is complicated.
Unless there are overriding performance concerns, the **<span
class="application">rm</span>** program should be used, with the `-rf`
and `--` options.

## Compensating for Unsafe File Creation

There are two ways to make a function or program which excepts a file
name safe for use with temporary files. See [Creating Safe
Processes](Tasks-Processes#process-creation)
for details on subprocess creation.

-   Create a temporary directory and place the file there. If possible,
    run the program in a subprocess which uses the temporary directory
    as its current directory, with a restricted environment. Use
    generated names for all files in that temporary directory. (See
    [Temporary
    Directories](#obtaining-the-location-of-temporary-directory).)

-   Create the temporary file and pass the generated file name to the
    function or program. This only works if the function or program can
    cope with a zero-length existing file. It is safe only under
    additional assumptions:

    -   The function or program must not create additional files whose
        name is derived from the specified file name or are otherwise
        predictable.

    -   The function or program must not delete the file before
        processing it.

    -   It must not access any existing files in the same directory.

        It is often difficult to check whether these additional
        assumptions are matched, therefore this approach is not
        recommended.

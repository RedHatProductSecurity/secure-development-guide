- [File System Manipulation](#file-system-manipulation)
  - [Working with Files and Directories Owned by Other Users](#working-with-files-and-directories-owned-by-other-users)
  - [Accessing the File System as a Different User](#accessing-the-file-system-as-a-different-user)
  - [File System Limits](#file-system-limits)
  - [File system features](#file-system-features)
  - [Checking Free Space](#checking-free-space)
# File System Manipulation

In this chapter, we discuss general file system manipulation, with a
focus on access files and directories to which an other, potentially
untrusted user has write access.

Temporary files are covered in their own chapter, [Temporary
Files](tasks/Tasks-Temporary_Files.xml#chap-Defensive_Coding-Tasks-Temporary_Files).

## Working with Files and Directories Owned by Other Users

Sometimes, it is necessary to operate on files and directories owned by
other (potentially untrusted) users. For example, a system administrator
could remove the home directory of a user, or a package manager could
update a file in a directory which is owned by an application-specific
user. This differs from accessing the file system as a specific user;
see [Accessing the File System as a Different
User](#sect-Defensive_Coding-Tasks-File_System-Foreign).

Accessing files across trust boundaries faces several challenges,
particularly if an entire directory tree is being traversed:

1.  Another user might add file names to a writable directory at any
    time. This can interfere with file creation and the order of names
    returned by `readdir`.

2.  Merely opening and closing a file can have side effects. For
    instance, an automounter can be triggered, or a tape device rewound.
    Opening a file on a local file system can block indefinitely, due to
    mandatory file locking, unless the `O_NONBLOCK` flag is specified.

3.  Hard links and symbolic links can redirect the effect of file system
    operations in unexpected ways. The `O_NOFOLLOW` and
    `AT_SYMLINK_NOFOLLOW` variants of system calls only affected final
    path name component.

4.  The structure of a directory tree can change. For example, the
    parent directory of what used to be a subdirectory within the
    directory tree being processed could suddenly point outside that
    directory tree.

Files should always be created with the `O_CREAT` and `O_EXCL` flags, so
that creating the file will fail if it already exists. This guards
against the unexpected appearance of file names, either due to creation
of a new file, or hard-linking of an existing file. In multi-threaded
programs, rather than manipulating the umask, create the files with mode
`000` if possible, and adjust it afterwards with `fchmod`.

To avoid issues related to symbolic links and directory tree
restructuring, the “`at`” variants of system calls have to be used (that
is, functions like `openat`, `fchownat`, `fchmodat`, and `unlinkat`,
together with `O_NOFOLLOW` or `AT_SYMLINK_NOFOLLOW`). Path names passed
to these functions must have just a single component (that is, without a
slash). When descending, the descriptors of parent directories must be
kept open. The missing `opendirat` function can be emulated with
`openat` (with an `O_DIRECTORY` flag, to avoid opening special files
with side effects), followed by `fdopendir`.

If the “`at`” functions are not available, it is possible to emulate
them by changing the current directory. (Obviously, this only works if
the process is not multi-threaded.) `fchdir` has to be used to change
the current directory, and the descriptors of the parent directories
have to be kept open, just as with the “`at`”-based approach.
`chdir("…​")` is unsafe because it might ascend outside the intended
directory tree.

This “`at`” function emulation is currently required when manipulating
extended attributes. In this case, the `lsetxattr` function can be used,
with a relative path name consisting of a single component. This also
applies to SELinux contexts and the `lsetfilecon` function.

Currently, it is not possible to avoid opening special files **and**
changes to files with hard links if the directory containing them is
owned by an untrusted user. (Device nodes can be hard-linked, just as
regular files.) `fchmodat` and `fchownat` affect files whose link count
is greater than one. But opening the files, checking that the link count
is one with `fstat`, and using `fchmod` and `fchown` on the file
descriptor may have unwanted side effects, due to item 2 above. When
creating directories, it is therefore important to change the ownership
and permissions only after it has been fully created. Until that point,
file names are stable, and no files with unexpected hard links can be
introduced.

Similarly, when just reading a directory owned by an untrusted user, it
is currently impossible to reliably avoid opening special files.

There is no workaround against the instability of the file list returned
by `readdir`. Concurrent modification of the directory can result in a
list of files being returned which never actually existed on disk.

Hard links and symbolic links can be safely deleted using `unlinkat`
without further checks because deletion only affects the name within the
directory tree being processed.

## Accessing the File System as a Different User

This section deals with access to the file system as a specific user.
This is different from accessing files and directories owned by a
different, potentially untrusted user; see [Accessing the File System as
a Different User](#sect-Defensive_Coding-Tasks-File_System-Foreign).

One approach is to spawn a child process which runs under the target
user and group IDs (both effective and real IDs). Note that this child
process can block indefinitely, even when processing regular files only.
For example, a special FUSE file system could cause the process to hang
in uninterruptible sleep inside a `stat` system call.

An existing process could change its user and group ID using `setfsuid`
and `setfsgid`. (These functions are preferred over `seteuid` and
`setegid` because they do not allow the impersonated user to send
signals to the process.) These functions are not thread safe. In
multi-threaded processes, these operations need to be performed in a
single-threaded child process. Unexpected blocking may occur as well.

It is not recommended to try to reimplement the kernel permission checks
in user space because the required checks are complex. It is also very
difficult to avoid race conditions during path name resolution.

## File System Limits

For historical reasons, there are preprocessor constants such as
`PATH_MAX`, `NAME_MAX`. However, on most systems, the length of
canonical path names (absolute path names with all symbolic links
resolved, as returned by `realpath` or `canonicalize_file_name`) can
exceed `PATH_MAX` bytes, and individual file name components can be
longer than `NAME_MAX`. This is also true of the `_PC_PATH_MAX` and
`_PC_NAME_MAX` values returned by `pathconf`, and the `f_namemax` member
of `struct
statvfs`. Therefore, these constants should not be used. This is also
reason why the `readdir_r` should never be used (instead, use
`readdir`).

You should not write code in a way that assumes that there is an upper
limit on the number of subdirectories of a directory, the number of
regular files in a directory, or the link count of an inode.

## File system features

Not all file systems support all features. This makes it very difficult
to write general-purpose tools for copying files. For example, a copy
operation intending to preserve file permissions will generally fail
when copying to a FAT file system.

-   Some file systems are case-insensitive. Most should be
    case-preserving, though.

-   Name length limits vary greatly, from eight to thousands of bytes.
    Path length limits differ as well. Most systems impose an upper
    bound on path names passed to the kernel, but using relative path
    names, it is possible to create and access files whose absolute path
    name is essentially of unbounded length.

-   Some file systems do not store names as fairly unrestricted byte
    sequences, as it has been traditionally the case on GNU systems.
    This means that some byte sequences (outside the POSIX safe
    character set) are not valid names. Conversely, names of existing
    files may not be representable as byte sequences, and the files are
    thus inaccessible on GNU systems. Some file systems perform Unicode
    canonicalization on file names. These file systems preserve case,
    but reading the name of a just-created file using `readdir` might
    still result in a different byte sequence.

-   Permissions and owners are not universally supported (and SUID/SGID
    bits may not be available). For example, FAT file systems assign
    ownership based on a mount option, and generally mark all files as
    executable. Any attempt to change permissions would result in an
    error.

-   Non-regular files (device nodes, FIFOs) are not generally available.

-   Only on some file systems, files can have holes, that is, not all of
    their contents is backed by disk storage.

-   `ioctl` support (even fairly generic functionality such as `FIEMAP`
    for discovering physical file layout and holes) is
    file-system-specific.

-   Not all file systems support extended attributes, ACLs and SELinux
    metadata. Size and naming restriction on extended attributes vary.

-   Hard links may not be supported at all (FAT) or only within the same
    directory (AFS). Symbolic links may not be available, either.
    Reflinks (hard links with copy-on-write semantics) are still very
    rare. Recent systems restrict creation of hard links to users which
    own the target file or have read/write access to it, but older
    systems do not.

-   Renaming (or moving) files using `rename` can fail (even when `stat`
    indicates that the source and target directories are located on the
    same file system). This system call should work if the old and new
    paths are located in the same directory, though.

-   Locking semantics vary among file systems. This affects advisory and
    mandatory locks. For example, some network file systems do not allow
    deleting files which are opened by any process.

-   Resolution of time stamps varies from two seconds to nanoseconds.
    Not all time stamps are available on all file systems. File creation
    time (**birth time**) is not exposed over the `stat`/`fstat`
    interface, even if stored by the file system.

## Checking Free Space

The `statvfs` and `fstatvfs` functions allow programs to examine the
number of available blocks and inodes, through the members `f_bfree`,
`f_bavail`, `f_ffree`, and `f_favail` of `struct statvfs`. Some file
systems return fictional values in the `f_ffree` and `f_favail` fields,
so the only reliable way to discover if the file system still has space
for a file is to try to create it. The `f_bfree` field should be
reasonably accurate, though.

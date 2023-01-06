- [Cryptography](#cryptography)
  - [Primitives](#primitives)
  - [Randomness](#randomness)
  - [Removing Sensitive information from memory](#removing-sensitive-information-from-memory)


# Cryptography

## Primitives

Choosing from the following cryptographic primitives is recommended:

-   RSA with 2048-bit keys and OAEP or PSS padding

-   AES-128 in CBC mode

-   AES-128 in GCM mode

-   AES-256 in CBC mode

-   AES-256 in GCM mode

-   SHA-256

-   HMAC-SHA-256

-   HMAC-SHA-1

Other cryptographic algorithms can be used if they are required for interoperability with existing software:

-   RSA with key sizes larger than 1024 and legacy padding

-   AES-192

-   3DES (triple DES, with two or three 56-bit keys), but strongly discouraged

-   RC4 (but very, very strongly discouraged)

-   SHA-1

-   HMAC-MD5

<div class="important">

<div class="title">

Important

</div>

These primitives are difficult to use in a secure way. Custom implementation of security protocols should be avoided.
For protecting confidentiality and integrity of network transmissions, TLS should be used ([Transport Layer
Security](features/Features-TLS.xml#chap-Defensive_Coding-TLS)).

In particular, when using AES in CBC mode, it is necessary to add integrity checking by other means, preferably using
HMAC-SHA-256 and **after** encryption (that is, on the encrypted cipher text). For AES in GCM mode, correct construction
of nonces is absolutely essential.

</div>

## Randomness

The following facilities can be used to generate unpredictable and non-repeating values. When these functions are used
without special safeguards, each individual random value should be at least 12 bytes long.

-   `PK11_GenerateRandom` in the NSS library (usable for high data rates)

-   `RAND_bytes` in the OpenSSL library (usable for high data rates)

-   `gnutls_rnd` in GNUTLS, with `GNUTLS_RND_RANDOM` as the first argument (usable for high data rates)

-   `java.security.SecureRandom` in Java (usable for high data rates)

-   The `secrets` module in Python. Older versions of Python (pre 3.6) can use `os.urandom`

-   The `getrandom` system call since glibc 2.25

-   The `getentropy` call since glibc 2.25

-   Reading from the `/dev/urandom` character device

All these functions should be non-blocking, and they should not wait until physical randomness becomes available. (Some
cryptography providers for Java can cause `java.security.SecureRandom` to block, however.) Those functions which do not
obtain all bits directly from `/dev/urandom` are suitable for high data rates because they do not deplete the
system-wide entropy pool.

<div class="important">

<div class="title">

Difficult to use API

</div>

Both `RAND_bytes` and `PK11_GenerateRandom` have three-state return values (with conflicting meanings). Careful error
checking is required. Please review the documentation when using these functions.

</div>

<div class="important">

<div class="title">

Difficult to use API

</div>

The `getrandom` system call has three-state return values, hence requires careful error checking.

It was introduced in Linux kernel 3.17, but before glibc 2.25 no API wrappers were provided. As such one could only use
it via the syscall interface as `syscall(SYS_getrandom, (void*)dest, (size_t)size, (unsigned int)0)`. For portable code
targeting multiple kernel versions one has to check for the function beingavailable on run-time, and switch to another
facility if the running kernel does not support this call.

</div>

Other sources of randomness should be considered predictable.

Generating randomness for cryptographic keys in long-term use may need different steps and is best left to cryptographic
libraries.

## Removing Sensitive information from memory

Sensitive data such as password, cryptographic keys etc, should be removed from memory as soon as possible, once this
information is no longer required.

However compiler optimizations make this erasure operation difficult, since the compiler deems this code as unnecessary
and often removes it from the compiled binary. For example a call to memset or a loop which zero’s out each byte of an
array may be optimized out during compilation.

This problem can be addressed by using `explicit_bzero()`. Calls to this function are never optimized by the compiler.

However, as per the `explicit_bzero()` documentation there are some things to consider:

-   The `explicit_bzero()` function does not guarantee that sensitive data is completely erased from memory. For
    example, there may be copies of the sensitive data in a register and in "scratch" stack areas. The
    `explicit_bzero()` function is not aware of these copies, and can’t erase them.

-   In some circumstances, `explicit_bzero()` can decrease security. If the compiler determined that the variable
    containing the sensitive data could be optimized to be stored in a register (because it is small enough to fit in a
    register, and no operation other than the `explicit_bzero()` call would need to take the address of the variable),
    then the `explicit_bzero()` call will force the data to be copied from the register to a location in RAM that is
    then immediately erased (while the copy in the register remains unaffected). The problem here is that data in RAM is
    more likely to be exposed by a bug than data in a register, and thus the `explicit_bzero()` call creates a brief
    time window where the sensitive data is more vulnerable than it would otherwise have been if no attempt had been
    made to erase the data.

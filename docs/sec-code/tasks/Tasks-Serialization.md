Unresolved directive in Tasks-Serialization.adoc -
include::partial$entities.adoc\[\]

# Serialization and Deserialization

Protocol decoders and file format parsers are often the most-exposed
part of an application because they are exposed with little or no user
interaction and before any authentication and security checks are made.
They are also difficult to write robustly in languages which are not
memory-safe.

## Recommendations for Manually-written Decoders

For C and C++, the advice in [Recommendations for Pointers and Array
Handling](programming-languages/C.xml#sect-Defensive_Coding-C-Pointers)
applies. In addition, avoid non-character pointers directly into input
buffers. Pointer misalignment causes crashes on some architectures.

When reading variable-sized objects, do not allocate large amounts of
data solely based on the value of a size field. If possible, grow the
data structure as more data is read from the source, and stop when no
data is available. This helps to avoid denial-of-service attacks where
little amounts of input data results in enormous memory allocations
during decoding. Alternatively, you can impose reasonable bounds on
memory allocations, but some protocols do not permit this.

## Protocol Design

Binary formats with explicit length fields are more difficult to parse
robustly than those where the length of dynamically-sized elements is
derived from sentinel values. A protocol which does not use length
fields and can be written in printable ASCII characters simplifies
testing and debugging. However, binary protocols with length fields may
be more efficient to parse.

In new datagram-oriented protocols, unique numbers such as sequence
numbers or identifiers for fragment reassembly (see
[Fragmentation](#sect-Defensive_Coding-Tasks-Serialization-Fragmentation))
should be at least 64 bits large, and really should not be smaller than
32 bits in size. Protocols should not permit fragments with overlapping
contents.

## Fragmentation

Some serialization formats use frames or protocol data units (PDUs) on
lower levels which are smaller than the PDUs on higher levels. With such
an architecture, higher-level PDUs may have to be **fragmented** into
smaller frames during serialization, and frames may need **reassembly**
into large PDUs during deserialization.

Serialization formats may use conceptually similar structures for
completely different purposes, for example storing multiple layers and
color channels in a single image file.

When fragmenting PDUs, establish a reasonable lower bound for the size
of individual fragments (as large as possible—limits as low as one or
even zero can add substantial overhead). Avoid fragmentation if at all
possible, and try to obtain the maximum acceptable fragment length from
a trusted data source.

When implementing reassembly, consider the following aspects.

-   Avoid allocating significant amount of resources without proper
    authentication. Allocate memory for the unfragmented PDU as more and
    more and fragments are encountered, and not based on the initially
    advertised unfragmented PDU size, unless there is a sufficiently low
    limit on the unfragmented PDU size, so that over-allocation cannot
    lead to performance problems.

-   Reassembly queues on top of datagram-oriented transports should be
    bounded, both in the combined size of the arrived partial PDUs
    waiting for reassembly, and the total number of partially
    reassembled fragments. The latter limit helps to reduce the risk of
    accidental reassembly of unrelated fragments, as it can happen with
    small fragment IDs (see [Fragment
    IDs](#sect-Defensive_Coding-Tasks-Serialization-Fragmentation-ID)).
    It also guards to some extent against deliberate injection of
    fragments, by guessing fragment IDs.

-   Carefully keep track of which bytes in the unfragmented PDU have
    been covered by fragments so far. If message reordering is a
    concern, the most straightforward data structure for this is an
    array of bits, with one bit for every byte (or other atomic unit) in
    the unfragmented PDU. Complete reassembly can be determined by
    increasing a counter of set bits in the bit array as the bit array
    is updated, taking overlapping fragments into consideration.

-   Reject overlapping fragments (that is, multiple fragments which
    provide data at the same offset of the PDU being fragmented), unless
    the protocol explicitly requires accepting overlapping fragments.
    The bit array used for tracking already arrived bytes can be used
    for this purpose.

-   Check for conflicting values of unfragmented PDU lengths (if this
    length information is part of every fragment) and reject fragments
    which are inconsistent.

-   Validate fragment lengths and offsets of individual fragments
    against the unfragmented PDU length (if they are present). Check
    that the last byte in the fragment does not lie after the end of the
    unfragmented PDU. Avoid integer overflows in these computations (see
    [Recommendations for Integer
    Arithmetic](programming-languages/C.xml#sect-Defensive_Coding-C-Arithmetic)).

### Fragment IDs

If the underlying transport is datagram-oriented (so that PDUs can be
reordered, duplicated or be lost, like with UDP), fragment reassembly
needs to take into account endpoint addresses of the communication
channel, and there has to be some sort of fragment ID which identifies
the individual fragments as part of a larger PDU. In addition, the
fragmentation protocol will typically involve fragment offsets and
fragment lengths, as mentioned above.

If the transport may be subject to blind PDU injection (again, like
UDP), the fragment ID must be generated randomly. If the fragment ID is
64 bit or larger (strongly recommended), it can be generated in a
completely random fashion for most traffic volumes. If it is less than
64 bits large (so that accidental collisions can happen if a lot of PDUs
are transmitted), the fragment ID should be incremented sequentially
from a starting value. The starting value should be derived using a
HMAC-like construction from the endpoint addresses, using a long-lived
random key. This construction ensures that despite the limited range of
the ID, accidental collisions are as unlikely as possible. (This will
not work reliable with really short fragment IDs, such as the 16 bit IDs
used by the Internet Protocol.)

## Library Support for Deserialization

There are too many subtleties when dealing with Deserialization to be
discussed here. A more detailed and updated guide is available as [OWASP
Deserialization Cheat
Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Deserialization_Cheat_Sheet.html).

## XML Serialization

### External References

XML documents can contain external references. They can occur in various
places.

-   In the DTD declaration in the header of an XML document:

    ``` xml
    <!DOCTYPE html PUBLIC
      "-//W3C//DTD XHTML 1.0 Transitional//EN"
      "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    ```

-   In a namespace declaration:

    ``` xml
    <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema">
    ```

-   In an entity definition:

    ``` xml
    <!ENTITY sys SYSTEM "http://www.example.com/ent.adoc[]>
    <!ENTITY pub PUBLIC "-//Example//Public Entity//EN"
      "http://www.example.com/pub-ent.adoc[]>
    ```

-   In a notation:

    ``` xml
    <!NOTATION not SYSTEM "../not.adoc[]>
    ```

Originally, these external references were intended as unique
identifiers, but by many XML implementations, they are used for locating
the data for the referenced element. This causes unwanted network
traffic, and may disclose file system contents or otherwise unreachable
network resources, so this functionality should be disabled.

Depending on the XML library, external referenced might be processed not
just when parsing XML, but also when generating it.

### Entity Expansion

When external DTD processing is disabled, an internal DTD subset can
still contain entity definitions. Entity declarations can reference
other entities. Some XML libraries expand entities automatically, and
this processing cannot be switched off in some places (such as attribute
values or content models). Without limits on the entity nesting level,
this expansion results in data which can grow exponentially in length
with size of the input. (If there is a limit on the nesting level, the
growth is still polynomial, unless further limits are imposed.)

Consequently, the processing internal DTD subsets should be disabled if
possible, and only trusted DTDs should be processed. If a particular XML
application does not permit such restrictions, then application-specific
limits are called for.

### XInclude Processing

XInclude processing can reference file and network resources and include
them into the document, much like external entity references. When
parsing untrusted XML documents, XInclude processing should be turned
off.

XInclude processing is also fairly complex and may pull in support for
the XPointer and XPath specifications, considerably increasing the
amount of code required for XML processing.

### Algorithmic Complexity of XML Validation

DTD-based XML validation uses regular expressions for content models.
The XML specification requires that content models are deterministic,
which means that efficient validation is possible. However, some
implementations do not enforce determinism, and require exponential (or
just polynomial) amount of space or time for validating some
DTD/document combinations.

XML schemas and RELAX NG (via the `xsd:` prefix) directly support
textual regular expressions which are not required to be deterministic.

### Using Expat for XML parsing

By default, Expat does not try to resolve external IDs, so no steps are
required to block them. However, internal entity declarations are
processed. Installing a callback which stops parsing as soon as such
entities are encountered disables them, see
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-Expat-EntityDeclHandler).
Expat does not perform any validation, so there are no problems related
to that.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-Expat-EntityDeclHandler.adoc[]
```

This handler must be installed when the `XML_Parser` object is created
([example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-Expat-Create)).

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-Expat-Create.adoc[]
```

It is also possible to reject internal DTD subsets altogether, using a
suitable `XML_StartDoctypeDeclHandler` handler installed with
`XML_SetDoctypeDeclHandler`.

### Using Qt for XML Parsing

The XML component of Qt, QtXml, does not resolve external IDs by
default, so it is not required to prevent such resolution. Internal
entities are processed, though. To change that, a custom
`QXmlDeclHandler` and `QXmlSimpleReader` subclasses are needed. It is
not possible to use the `QDomDocument::setContent(const QByteArray
&)` convenience methods.

[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-Qt-NoEntityHandler)
shows an entity handler which always returns errors, causing parsing to
stop when encountering entity declarations.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-Qt-NoEntityHandler.adoc[]
```

This handler is used in the custom `QXmlReader` subclass in
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-Qt-NoEntityReader).
Some parts of QtXml will call the `setDeclHandler(QXmlDeclHandler *)`
method. Consequently, we prevent overriding our custom handler by
providing a definition of this method which does nothing. In the
constructor, we activate namespace processing; this part may need
adjusting.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-Qt-NoEntityReader.adoc[]
```

Our `NoEntityReader` class can be used with one of the overloaded
`QDomDocument::setContent` methods.
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-Qt-QDomDocument)
shows how the `buffer` object (of type `QByteArray`) is wrapped as a
`QXmlInputSource`. After calling the `setContent` method, you should
check the return value and report any error.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-Qt-QDomDocument.adoc[]
```

### Using OpenJDK for XML Parsing and Validation

OpenJDK contains facilities for DOM-based, SAX-based, and StAX-based
document parsing. Documents can be validated against DTDs or XML
schemas.

The approach taken to deal with entity expansion differs from the
general recommendation in [Entity
Expansion](#sect-Defensive_Coding-Tasks-Serialization-XML-Entities). We
enable the the feature flag
`javax.xml.XMLConstants.FEATURE_SECURE_PROCESSING`, which enforces
heuristic restrictions on the number of entity expansions. Note that
this flag alone does not prevent resolution of external references
(system IDs or public IDs), so it is slightly misnamed.

In the following sections, we use helper classes to prevent external ID
resolution.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-OpenJDK-NoEntityResolver.adoc[]
```

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-OpenJDK-NoResourceResolver.adoc[]
```

[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK-Imports)
shows the imports used by the examples.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-OpenJDK-Imports.adoc[]
```

#### DOM-based XML parsing and DTD validation in OpenJDK

This approach produces a `org.w3c.dom.Document` object from an input
stream.
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK_Parse-DOM)
use the data from the `java.io.InputStream` instance in the
`inputStream` variable.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-OpenJDK_Parse-DOM.adoc[]
```

External entity references are prohibited using the `NoEntityResolver`
class in
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK-NoEntityResolver).
Because external DTD references are prohibited, DTD validation (if
enabled) will only happen against the internal DTD subset embedded in
the XML document.

To validate the document against an external DTD, use a
`javax.xml.transform.Transformer` class to add the DTD reference to the
document, and an entity resolver which whitelists this external
reference.

#### XML Schema Validation in OpenJDK

[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK_Parse-XMLSchema_SAX)
shows how to validate a document against an XML Schema, using a
SAX-based approach. The XML data is read from an `java.io.InputStream`
in the `inputStream` variable.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-OpenJDK_Parse-XMLSchema_SAX.adoc[]
```

The `NoResourceResolver` class is defined in
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK-NoResourceResolver).

If you need to validate a document against an XML schema, use the code
in
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK_Parse-DOM)
to create the document, but do not enable validation at this point. Then
use
[example_title](#ex-Defensive_Coding-Tasks-Serialization-XML-OpenJDK_Parse-XMLSchema_DOM)
to perform the schema-based validation on the `org.w3c.dom.Document`
instance `document`.

``` java
Unresolved directive in Tasks-Serialization.adoc - include::example$Tasks-Serialization-XML-OpenJDK_Parse-XMLSchema_DOM.adoc[]
```

#### Other XML Parsers in OpenJDK

OpenJDK contains additional XML parsing and processing facilities. Some
of them are insecure.

The class `java.beans.XMLDecoder` acts as a bridge between the Java
object serialization format and XML. It is close to impossible to
securely deserialize Java objects in this format from untrusted inputs,
so its use is not recommended, as with the Java object serialization
format itself. See [Library Support for
Deserialization](#sect-Defensive_Coding-Tasks-Serialization-Library).

## Protocol Encoders

For protocol encoders, you should write bytes to a buffer which grows as
needed, using an exponential sizing policy. Explicit lengths can be
patched in later, once they are known. Allocating the required number of
bytes upfront typically requires separate code to compute the final
size, which must be kept in sync with the actual encoding step, or
vulnerabilities may result. In multi-threaded code, parts of the object
being deserialized might change, so that the computed size is out of
date.

You should avoid copying data directly from a received packet during
encoding, disregarding the format. Propagating malformed data could
enable attacks on other recipients of that data.

When using C or C++ and copying whole data structures directly into the
output, make sure that you do not leak information in padding bytes
between fields or at the end of the `struct`.

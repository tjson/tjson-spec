%%%

    Title = "Tagged JavaScript Object Notation (TJSON) Data Interchange Format"
    abbrev = "TJSON Data Interchange Format"
    category = "info"
    docName = "draft-tjson-spec"
    
    date = 2016-11-03T20:00:00Z
    
    [[author]]
    initials = "T. "
    surname = "Arcieri"
    fullname = "Tony Arcieri"

    [[author]]
    initials = "B. "
    surname = "Laurie"
    fullname = "Ben Laurie"

%%%

.# Abstract

Tagged JavaScript Object Notation (TJSON) is a data interchange format which is
backwards compatible with the JavaScript Object Notation (JSON) [@!RFC7159]
format. It adds first-class type annotations ("tags") for supporting a richer
set of types within JSON documents.

{mainmatter}

# Introduction

Tagged JavaScript Object Notation (TJSON) is a set of backwards-compatible
extensions to JavaScript Object Notation (JSON) [@!RFC7159] which enriches
the format with additional types beyond those originally specified.

TJSON supports six scalar types:

* Strings
* Binary Data
* Integers (signed/unsigned)
* Floating points
* Timestamps
* JSON values (true/false/nil)

It supports two non-scalar types:

* Objects
* Arrays

TJSON provides backwards-compatible self-describing type annotations to JSON
in the form of postfix tags on object member names.

To extend JSON with additional types in a backwards-compatible manner, TJSON
adds a special mandatory "tag" to each object member name which identifies the
type and encoding format of the member value. A tag consists of a colon ":"
delimiter followed by one or more alphanumeric characters which comprise the
tag name. All object member names in TJSON MUST have a valid tag.

TJSON is intended to simplify transcoding documents from other interchange
formats which have a type system rich enough to include a binary data format
in addition to strings, and to improve the ability authenticate JSON documents.

## Conventions Used in This Document

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [@!RFC2119].

# TJSON Grammar

TJSON relies on JSON as the basis of its grammar. All TJSON documents are valid
JSON documents (however the reverse is not true). The TJSON grammar can thus
be seen as an extension of the JSON grammar, but one implemented in a
backwards-compatible way.

## String Grammar

The main grammatical addition of TJSON is the addition of a postfix type
annotation, or "tag", on the member names of all objects, which are string
literals. Every member name MUST have a tag prefix in TJSON. Member names
in TJSON are described by the following grammar:

    <member> ::= <tagged-string> <name-separator> <value>

    <tagged-string> ::= '"' *<char> ':' <tag> '"'

    <tag> ::= <object-tag> | <type-expression> | <scalar-tag>

    <type-expression> ::= <non-scalar-tag> '<' <tag> '>'

    <non-scalar-tag> ::= <alpha-upper> *<alphanumeric-lower>

    <scalar-tag> ::= <alpha-lower> *<alphanumeric-lower>

    <object-tag> ::= 'O'

    <alphanumeric-lower> ::= <alpha-lower> | <digit>

    <alpha-upper> ::= 'A' | 'B' | 'C' | 'D' | 'E' | 'F' | 'G' | 'H' | 
                      'I' | 'J' | 'K' | 'L' | 'M' | 'N' | 'O' | 'P' |
                      'Q' | 'R' | 'S' | 'T' | 'U' | 'V' | 'W' | 'X' |
                      'Y' | 'Z'

    <alpha-lower> ::= 'a' | 'b' | 'c' | 'd' | 'e' | 'f' | 'g' | 'h' | 
                      'i' | 'j' | 'k' | 'l' | 'm' | 'n' | 'o' | 'p' |
                      'q' | 'r' | 's' | 't' | 'u' | 'v' | 'w' | 'x' |
                      'y' | 'z'

    <digit> ::= '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'

The "member" pushdown (with a "tagged-string" instead of a "string"
as in JSON replaces the "member" pushdown as described in [@!RFC7159].
The "value" pushdown remains the same.

The "char" nonterminal is described in section 7 of [@!RFC7159].

TJSON uses the case of tag names to identify scalar types vs non-scalars:

* Scalars: lower-case tag names (e.g. "t")
* Non-scalars: capitalized tag names (e.g. "A")

The "object-tag" nonterminal has a special place in the grammar: as the
sole carrier of type information objects are the only nonterminal
which does not carry type information.

## Root Symbol

The root grammatical symbol of all TJSON documents is constrained to the
"object" nonterminal as described in [@!RFC7159]:

    <root> ::= <object>

TJSON uses objects to describe all further type information, so they MUST
be the top-level expression.

Documents which do not contain an object as the top-level element MUST be
rejected by parsers.

# Extended Types

The following section describes the extended types added to TJSON by embedding
them in string literals as described in section 2.1 of this document.

## Unicode Strings ("s")

The syntax for TJSON strings is grammatically identical to JSON, except per
section 2.1 of this document the string type MUST carry a mandatory tag
character, "s" indicating a Unicode string.

Unlike JSON, all Unicode Strings in TJSON MUST be valid UTF-8 [@!RFC3629].
No other Unicode encodings are valid for TJSON strings.

The following is an example of a Unicode String literal in TJSON:

    {"example:s":"Hello, world!"}

## Binary Data

TJSON provides first-class support for binary data stored in multiple
different encodings within a tagged string. Tags for binary data begin
with the "b" character followed by an alphanumeric identifier for a
specific format.

The preferred encoding is base64url ("b64"), which SHOULD be used by
default unless another encoding is explicitly specified at serialization
time.

The base16, base32, and base64url formats are mandatory to implement for all
TJSON parsers.

### base16 ("b16")

Base16 literals are identified by the "b16" tag, with an associated JSON
JSON string literal value containing base16-serialized binary data.

The base16 format (a.k.a. hexadecimal) is described in [@!RFC4648]. All base16
strings in TJSON MUST be lower case.

The following is an example of a base16 string literal in TJSON:

    {"example:b16":"48656c6c6f2c20776f726c6421"}

This decodes to an object with an "example" key whose value is the equivalent
of the ASCII string: "Hello, world!"

### base32 ("b32")

Base32 literals are identified by the "b32" tag, with an associated JSON
JSON string literal value containing base32-serialized binary data.

The base32 format is described in [@!RFC4648]. All base32 strings in TJSON
MUST be lower case, and MUST NOT include any padding with the '=' character.
TJSON parsers MUST reject any documents containing upper case base32 characters
or padding.

The following is an example of a base32 string literal in TJSON:

    {"example:b32":"jbswy3dpfqqho33snrscc"}

This decodes to an object with an "example" key whose value is the equivalent
of the ASCII string: "Hello, world!"

### base64url ("b64")

Base64url literals are identified by the "b" or "b64" tags, with an
associated JSON string literal value containing base64url-serialized binary
data.

The base64url format is described in [@!RFC4648]. All base64url strings in
TJSON MUST NOT include any padding with the '=' character. TJSON parsers MUST
reject any documents containing padded base64url strings.

When serializing binary data as TJSON, encoders SHOULD use the "b" tag to
indicate binary data unless another format has been explicitly specified.

The following is an example of a base64url string literal in TJSON:

    {"example:b64":"SGVsbG8sIHdvcmxkIQ"}

The following is the same document using the shorter "b" tag:

    {"example:b":"SGVsbG8sIHdvcmxkIQ"}

This decodes to an object with an "example" key whose value is the equivalent
of the ASCII string: "Hello, world!"

Only the base64url format is supported. The non-URL safe form of base64
is not supported and MUST be rejected by parsers.

## Integers

TJSON provides standard syntax for representing integers as tagged strings,
which can express a larger range of values than the `[-(2**53)+1, (2**53)-1]`
range defined as interoperable in [@!RFC7159].

Both signed and unsigned integers are supported and provide the same ranges
as 64-bit integers.

### Signed Integers ("i")

Signed integer literals are identified by the "i" tag, with an associated
JSON string literal value containing the string representation of a valid
JSON integer literal, with an optional minus ("-") character.

Conforming TJSON parsers MUST be capable of supporting the full 64-bit signed
integer range `[-(2**63), (2**63)-1]` for this type.

Integers outside this range MUST be rejected.

### Unsigned Integers ("u")

Unsigned integer literals are identified by the "u" tag, with an associated
JSON string literal value containing the string representation of a valid
JSON integer literal. The minus ("-") character is expressly disallowed and
parsers MUST reject documents containing it in an unsigned integer expression.

Conforming TJSON parsers MUST be capable of supporting the full 64-bit unsigned
integer range `[0, (2**64)−1]` for this type.

## Timestamps ("t")

TJSON natively supports a timestamp type whose syntax is a subset of that
provided by [@!RFC3339]. Specifically, TJSON timestamps MUST use only the
upper-case UTC time zone identifier "Z" (i.e. times MUST be Z-normalized).
No other time zone identifiers are allowed except "Z" and parsers MUST NOT
allow them.

The following is an example of a TJSON timestamp:

    {"example:t":"2016-10-02T07:31:51Z"}

TJSON libraries SHOULD convert these timestamps to a native date/time type.

## Arrays ("A")

Arrays are a non-scalar and therefore use an upper case tag name as described
in section 2.1. 

The "A" tag, with an associated JSON array value, identifies a TJSON array.
Non-scalars are parameterized by the types they contain, so fully identifying
an array depends on its contents.

The following is an example of a TJSON array containing integers:

    {"example:A<i>": ["1", "2", "3"]}

The following is an example of a two dimensional array containing integers:

    {"example:A<A<i>>:" [["1", "2"], ["3", "4"], ["5", "6"]]}

## Objects ("O")

Type information MUST be present in all object member names (i.e. all member
names must be tagged). Parsers MUST reject objects with untagged members.

When embedded within another non-scalar type such as an array, objects
are identified by the tag "O". Objects self-identify their types, so do
not need to be parameterized in type expressions.

The following is an example of an array of objects:

    {"example:A<O>": [{"a:i": "1"}, {"b:i": "2"}]}

Object member names MUST be unique in TJSON. Repeated use of the same
name for more than one member MUST be rejected by TJSON parsers.

## Floating Points

All numeric literals which are not represented as tagged strings MUST be
treated as floating points under TJSON. This is already the default behavior
of many JSON libraries.

{backmatter}

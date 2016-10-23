%%%

    Title = "Tagged JavaScript Object Notation (TJSON) Data Interchange Format"
    abbrev = "TJSON Data Interchange Format"
    category = "info"
    docName = "draft-tjson-spec"
    
    date = 2016-10-02T20:00:00Z
    
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
extensions to JavaScript Object Notation (JSON) [@!RFC7159] which enrich
the set of types the format is able to express.

TJSON can represent six primitive types (strings, binary data, integers,
floating points, datetimes, and null) and two structured types (objects and
arrays).

To extend JSON with additional types in a backwards-compatible manner,
TJSON adds a special mandatory "tag" to each JSON string which identifies
the data type and, optionally, encoding format. A tag consists of one
or more alphanumeric characters, followed by the colon ":" character.
All strings in TJSON MUST have a valid tag prefix.

TJSON is intended to simplify transcoding documents from other interchange
formats which disambiguate strings from binary data, and also improve the
ability to both canonicalize and authenticate JSON documents.

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

The main grammatical addition of TJSON is a tag prefix on string literals. Every
string literal MUST have a tag prefix in TJSON. Strings literals in TJSON are
described by the following grammar:

    <tagged-string> ::= quotation-mark tag *char quotation-mark

    <tag> ::= <alpha> *<alphanumeric> ':'

    <alpha> ::= 'a' | 'b' | 'c' | 'd' | 'e' | 'f' | 'g' | 'h' | 'i' |
                'j' | 'k' | 'l' | 'm' | 'n' | 'o' | 'p' | 'q' | 'r' |
                's' | 't' | 'u' | 'v' | 'w' | 'x' | 'y' | 'z'

    <digit> ::= '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'

    <alphanumeric> ::= <alpha> | <digit>

The tagged-string pushdown replaces the string pushdown in JSON as described in
[@!RFC7159].

The quotation-mark and char pushdowns are described in section 7 of [@!RFC7159].

TJSON places a maximum length of 4 bytes on tag, including the ':' character.

## Root Symbol

The root grammatical symbol of all TJSON documents is constrained to the
following nonterminals as described in [@!RFC7159]:

    <root> ::= <object> | <array>

Documents which do not contain an object or array as the toplevel element
MUST be rejected by parsers.

# Extended Types

The following section describes the extended types added to TJSON by embedding
them in string literals as described in section 2.1 of this document.

## UTF-8 Strings ("s:")

The syntax for TJSON strings is grammatically identical to JSON, except per
section 2.1 of this document the string type MUST carry a mandatory tag
character, "s:" indicating a UTF-8 String. Unlike JSON, all Unicode Strings
in TJSON MUST be valid UTF-8 [@!RFC3629]. Other Unicode encodings are
expressly not supported.

The following is an example of a UTF-8 String literal in TJSON:

    "s:Hello, world!"

## Binary Data

TJSON provides first-class support for binary data stored in multiple
different encodings within a tagged string. Tags for binary data begin
with the "b" character followed by an alphanumeric identifier for a
specific format.

The preferred encoding is base64url ("b64:"), which SHOULD be used by
default unless another encoding is explicitly specified at serialization
time.

The base16 and base64url formats are mandatory to implement for all TJSON
parsers.

### base16 ("b16:")

A base16 literal starts with the "b16:" tag, followed by a valid base16 string.
The base16 format (a.k.a. hexadecimal) is described in [@!RFC4648]. All base16
strings in TJSON MUST be lower case.

The following is an example of a base16 string literal in TJSON:

    "b16:48656c6c6f2c20776f726c6421"

This should decode to the equivalent of the ASCII string: "Hello, world!"

### base64url ("b64:")

A base64url literal starts with the "b64:" tag, followed by a valid base64url
string. The base64url format is described in [@!RFC4648]. All base64url strings
in TJSON MUST NOT include any padding with the '=' character. TJSON parsers
MUST reject any documents containing padded base64url strings.

The following is an example of a base64url string literal in TJSON:

    "b64:SGVsbG8sIHdvcmxkIQ"

This should decode to the equivalent of the ASCII string: "Hello, world!"

Only the base64url format is supported. The non-URL safe form of base64
is not supported and MUST be rejected by parsers.

## Integers

TJSON provides standard syntax for representing integers as tagged strings,
which can express a larger range of values than the `[-(2**53)+1, (2**53)-1]`
range defined as interoperable in [@!RFC7159].

Both signed and unsigned integers are supported and provide the same ranges
as 64-bit integers.

### Signed Integers ("i:")

A signed integer literal is represented as string with an "i:" tag, followed
by a valid JSON integer literal, with an optional minus ("-") character.

Conforming TJSON parsers MUST be capable of supporting the full 64-bit signed
integer range `[-(2**63), (2**63)-1]` for this type.

Integers outside this range MUST be rejected.

### Unsigned Integers ("u:")

An unsigned integer literal is represented as a string with a "u:" tag,
followed by a valid JSON integer literal. The minus ("-") character is
expressly disallowed and parsers MUST fail if it's present.

Conforming TJSON parsers MUST be capable of supporting the full 64-bit unsigned
integer range `[0, (2**64)−1]` for this type.

## Timestamps ("t:")

TJSON natively supports a timestamp type whose syntax is a subset of that
provided by [@!RFC3339]. Specifically, TJSON timestamps MUST use only the
upper-case UTC time zone identifier "Z". No other time zone identifiers are
allowed except "Z" and parsers MUST NOT allow them.

The following is an example of a TJSON timestamp:

    "t:2016-10-02T07:31:51Z"

TJSON libraries SHOULD convert these timestamps to a native datetime type.

# Handling of JSON types

Below are notes about how the processing of certain JSON types should be
handled under TJSON.

## Objects

TJSON constrains the allowable types for the names of object members to either
Unicode Strings or Binary Data.

All other types, such as integers, are expressly disallowed.

## Floating Points

All numeric literals which are not represented as tagged strings MUST be
treated as floating points under TJSON. This is already the default behavior
of many JSON libraries.

{backmatter}

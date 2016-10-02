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
backwards compatible with the JavaScript Object Notation (JSON)[ @!RFC7159]
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

{backmatter}

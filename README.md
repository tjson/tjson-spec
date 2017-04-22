# TJSON Specification [![Build Status][build-image]][build-link] [![Gitter][chat-image]][chat-link]

[build-image]: https://travis-ci.org/tjson/tjson-spec.svg?branch=master
[build-link]: https://travis-ci.org/tjson/tjson-spec
[chat-image]: https://img.shields.io/gitter/room/gitterHQ/gitter.svg
[chat-link]: https://gitter.im/tjson/Lobby

IETF-style specification for TJSON authored using [mmark].

[mmark]: https://github.com/miekg/mmark

```json
{
  "array-example:A<O>": [
    {
      "string-example:s": "foobar",
      "binary-example:b64": "QklOQVJZ",
      "float-example:f": 0.42,
      "int-example:i": "42",
      "timestamp-example:t": "2016-11-06T22:27:34Z",
      "value-example:v": true
    }
  ],
  "set-example:S<i>": [1, 2, 3]
}
```

## Specification Document

The file `draft-tjson-spec.md` contains the canonical copy of the TJSON
specification, authored with [mmark]. Text and HTML versions can be
produced using the [xml2rfc] tool.

An HTML rendering of the spec can be found at:

https://www.tjson.org/spec/

[xml2rfc]: https://xml2rfc.tools.ietf.org/

## Examples File

A machine-parsable annotated file `draft-tjson-examples.txt` is available at:

https://raw.githubusercontent.com/tjson/tjson-spec/master/draft-tjson-examples.txt

Instructions on how to parse the file are contained within the file itself.

This file contains test cases which both succeed and fail, and can be used for
implementing automated tests of TJSON parsers in a reusable manner.

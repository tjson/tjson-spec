# TJSON Specification [![Build Status](https://travis-ci.org/tjson/tjson-spec.svg?branch=master)](https://travis-ci.org/tjson/tjson-spec)

IETF-style specification for TJSON authored using [mmark].

[mmark]: https://github.com/miekg/mmark

## Specification Document

The file `draft-tjson-spec.md` contains the canonical copy of the TJSON
specification, authored with [mmark]. Text and HTML versions can be
produced using the [xml2rfc] tool.

[xml2rfc]: https://xml2rfc.tools.ietf.org/

## Examples File

A machine-parsable annotated file `draft-tjson-examples.txt` is available at:

https://raw.githubusercontent.com/tjson/tjson-spec/master/draft-tjson-examples.txt

Instructions on how to parse the file are contained within the file itself.

This file contains test cases which both succeed and fail, and can be used for
implementing automated tests of TJSON parsers in a reusable manner.

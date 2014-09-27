# CSV Spec

CSV is not a file format, it is typically a loose set of guidelines of how to
structure tabular data into a plain text string. As such there's an endless
amount of `*.csv` files floating around which are highly incompatible with
each other. The closest thing there is to a specification is
[RFC 4180](http://tools.ietf.org/html/rfc4180).


## Goals

This project is an attempt to summarize RFC 4180 and the information in the
[Comma-separated values (CSV)](http://en.wikipedia.org/wiki/Comma-separated_values)
Wikipedia article into a easy to understand format. The spec will also take
into account that the comma (`,`) character is not the only character used as
a field delimiter. Semi-colons (`;`), tabs (`\t`), and more are popular field
delimiter characters. As such the specification will more accurately be
describing a CSV-like structured data format.

We will also provide input/output test files that CSV parser/writer software
libraries can use to validate if they properly adhere to the rules laid out in
this specification. And if possible we will even try to provide code snippets
in various languages that attempts to automatically determine the delimiter
character used in any given input CSV-like formatted file/data.


## Roadmap

1. Write up core specification rules. _[in-progress]_
2. Create input/output test files covering all rules in the specification.
3. Create website for [csv-spec.org](http://csv-spec.org/).
4. Create linting tool as a NPM module, allowing easy validation of CSV
   data both client-side in a web browser, and server side via a command line
   tool.
5. Create automatic delimiter character detection code snippets in various
   programming languages which CSV parser developers can freely use to enhance
   their libraries.


## License

(The MIT license)

Copyright (c) 2014 Jim Myhrberg.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

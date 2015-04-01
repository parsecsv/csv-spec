# CSV Spec

CSV is not a file format, it is typically a loose set of guidelines of how to
structure tabular data into a plain text string. As such there's an endless
amount of `*.csv` files floating around which are highly incompatible with
each other. The closest thing there is to a specification is [RFC 4180][].

[rfc 4180]: http://tools.ietf.org/html/rfc4180


## Goals

This project is an attempt to summarize RFC 4180 and the information in the
[Comma-separated values (CSV)][csv] Wikipedia article into a easy to
understand format. The spec will also take into account that the comma (`,`)
character is not the only character used as a field delimiter. Semi-colons
(`;`), tabs (`\t`), and more are popular field delimiter characters. As such
the specification will more accurately be describing a CSV-like structured
data format.

[csv]: http://en.wikipedia.org/wiki/Comma-separated_values

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


## Terminology

- **Field** — A singular String value within a row.
- **Record** (or **Row**) — A collection of fields.
- **Column** — Fields from multiple rows at the same offset. For example the
  second column would be a list of the second field from every row.
- **Delimiter** — The character used to separate fields withing a
  row. Commonly this will be a comma (`,`), but semi-colons (`;`) or tabs
  (`\t`) are two other popular delimiter characters.
- **Header** — The first row is often used to contain the column names for all
  remaining rows. Header names would be used as key names when CSV data is
  converted to JSON for example.
- **Line Break** — Line breaks in CSV files should be CRLF (`\r\n`). In
  examples the `¬` character will be used to visually display line breaks.


## Rules

1. Each record is located on a separate line, each line ending with CRLF
   (`\r\n`). For example:

   CSV:

   ```csv
   aaa,bbb,ccc¬
   xxx,yyy,zzz¬
   ```

   JSON:

   ```json
   [
     ["aaa", "bbb", "ccc"],
     ["xxx", "yyy", "zzz"]
   ]
   ```


## License

[CC0 1.0 Universal](http://creativecommons.org/publicdomain/zero/1.0/)

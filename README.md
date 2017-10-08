CSV Spec 0.9.0-draft.0
======================

Summary
-------

CSV is not a file format, it is a loose set of guidelines of how to structure
tabular data into a plain text string. As such there's an endless amount of
`*.csv` files floating around which are highly incompatible with each other. The
closest thing there is to a specification is [RFC
4180](http://tools.ietf.org/html/rfc4180).

Goals
-----

This project is an attempt to summarize RFC 4180 and the information in the
[Comma-separated values
(CSV)](http://en.wikipedia.org/wiki/Comma-separated_values) Wikipedia article
into a easy to understand format. The spec will also take into account that the
comma (`,`) character is not the only character used as a field
delimiter. Semi-colons (`;`), tabs (`\t`), and more are popular field delimiter
characters. As such the specification will more accurately be describing a
CSV-like structured data format.

We will also provide input/output test files that CSV parser/writer software
libraries can use to validate if they properly adhere to the rules laid out in
this specification. And if possible we will even try to provide code snippets in
various languages that attempts to automatically determine the delimiter
character used in any given input CSV-like formatted file/data.

Roadmap
-------

1. Write up core specification rules. _[in-progress]_
2. Create input/output test files covering all rules in the specification.
3. Create website for [csv-spec.org](http://csv-spec.org/).
4. Create linting tool as a NPM module, allowing easy validation of CSV data
   both client-side in a web browser, and server side via a command line tool.
5. Create automatic delimiter character detection code snippets in various
   programming languages which CSV parser developers can freely use to enhance
   their libraries.

Terminology
-----------

- **Field** — A singular String value within a record.
- **Record** (or **Row**) — A collection of fields. This is often referred to as
  a "line", but a single record can span multiple text lines if a field within
  it contains one or more line breaks.
- **Delimiter** — The character used to separate fields withing a row. Commonly
  this will be a comma (`,`), but semi-colons (`;`) or tabs (`\t`) are two other
  popular delimiter characters.
- **Header** — The first row is often used to contain the column names for all
  remaining rows. Header names would be used as key names when CSV data is
  converted to JSON for example.
- **Line Break** — Line breaks in CSV files can be CRLF (`\r\n`), LF (`\n`), and
  even in rare cases CR (`\r`).
- **LF, CR, and CRLF** — Different types of line breaks, typically determined by
  the OS. Linux, OSX, and other *NIX operating systems generally use a line feed
  (LF or `\n`) character. Windows uses a carriage return (CR or `\r`) and a line
  feed character, effectively "CRLF" (`\r\n`).

CSV Format Specification
------------------------

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

These rules are mostly based on the corresponding section from [RFC
4180](http://tools.ietf.org/html/rfc4180#section-2), with minor changes,
clarifications and improved examples.

1. Each record starts at the beginning of its own line, and ends with a line
   break (shown as `¬`).

   CSV:

   ```csv
   aaa,bbb,ccc¬
   xxx,yyy,zzz¬
   ```

   JSON:

   ```json
   [ ["aaa", "bbb", "ccc"],
     ["xxx", "yyy", "zzz"] ]
   ```

2. Though it is RECOMMENDED, the last record in a file is not required to have a
   ending line break.

   CSV:

   ```csv
   aaa,bbb,ccc¬
   xxx,yyy,zzz
   ```

   JSON:

   ```json
   [ ["aaa", "bbb", "ccc"],
     ["xxx", "yyy", "zzz"] ]
   ```

3. There may be an OPTIONAL header line appearing as the first line of the file
   with the same format as normal records. This header will contain names
   corresponding to the fields in the file, and MUST contain the same number of
   fields as the records in the rest of the file.

   CSV:

   ```csv
   field_1,field_2,field_3¬
   aaa,bbb,ccc¬
   xxx,yyy,zzz¬
   ```

   JSON (ignoring headers):

   ```json
   [ ["field_1", "field_2", "field_3"],
     ["aaa", "bbb", "ccc"],
     ["xxx", "yyy", "zzz"] ]
   ```

   JSON (using headers):

   ```json
   [ {"field_1": "aaa", "field_2": "bbb", "field_3": "ccc"},
     {"field_1": "xxx", "field_2": "yyy", "field_3": "zzz"} ]
   ```

4. Within each record and the OPTIONAL header, there may be one or more fields,
   separated by a delimiter (normally a comma). Each record MUST contain the
   same number of fields throughout the file.

   CSV (invalid):

   ```csv
   aaa,bbb,ccc¬
   111,222,333,444¬
   xxx,yyy,zzz¬
   ```

5. The last field in a record MUST NOT be followed by a comma. This results in a
   additional field with nothing in it.

   CSV:

   ```csv
   aaa,bbb,ccc,¬
   xxx,yyy,zzz,¬
   ```

   JSON:

   ```json
   [ ["aaa", "bbb", "ccc", ""],
     ["xxx", "yyy", "zzz", ""] ]
   ```

6. Spaces are considered part of a field and MUST NOT be ignored.

   CSV:

   ```csv
   aaa ,  bbb , ccc¬
    xxx, yyy  ,zzz ¬
   ```

   JSON:

   ```json
   [ ["aaa ", "  bbb ", " ccc"],
     [" xxx", " yyy  ", "zzz "] ]
   ```

7. Fields containing line breaks (CRLF, LF, or CR), double quotes, or the
   delimiter character (normally a comma) MUST be enclosed in double-quotes.

   CSV:

   ```csv
   aaa,"b¬
   bb",ccc¬
   xxx,"y, yy",zzz¬
   ```

   JSON:

   ```json
   [ ["aaa", "b\r\nbb", "ccc"],
     ["xxx", "y, yy", "zzz"] ]
   ```

8. A double-quote appearing inside a field MUST be escaped by preceding it with
   another double quote, and the field itself MUST be enclosed in double quotes.

   CSV:

   ```csv
   aaa,"b""bb",ccc¬
   ```

   JSON:

   ```json
   [ ["aaa", "b\"bb", "ccc"] ]
   ```

9. When a field enclosed in double quotes has spaces before and/or after the
   double quotes, the spaces MUST be ignored, as the field starts and ends with
   the double quotes. However this is considered invalid formatting and the CSV
   parser SHOULD report some form of warning message.

   CSV:

   ```csv
   aaa,bbb,ccc¬
   xxx,  "y, yy" ,zzz¬
   ```

   JSON:

   ```json
   [ ["aaa", "bbb", "ccc"],
     ["xxx", "y, yy", "zzz"] ]
   ```

10. It is possible to enclose every field in double quotes even if they don't
    need to be enclosed. However it is RECOMMENDED to only enclose fields in
    double quotes that requires it.

    CSV:

    ```csv
    "aaa","bbb","ccc"¬
    "xxx",yyy,zzz¬
    ```

    JSON:

    ```json
    [ ["aaa", "bbb", "ccc"],
      ["xxx", "yyy", "zzz"] ]
    ```

11. All fields are always strings. CSV itself does not support type casting to
    integers, floats, booleans, or anything else. It is not a CSV library's
    responsibility to type cast input CSV data.

    If type casting is required, it is up to the developer using a specific CSV
    library to ensure types are correctly dealt with.

    Input JSON:

    ```json
    [ [10, true, 0.3, null, "aaa"],
      [11, false, 2.13, "", "bbb"] ]
    ```

    Output CSV:

    ```csv
    10,true,0.3,,aaa¬
    11,false,2.13,,bbb¬
    ```

    Output CSV parsed back to JSON:

    ```json
    [ ["10", "true", "0.3", "", "aaa"],
      ["11", "false", "2.13", "", "bbb"] ]
    ```

    At this point it is up to the developer themselves to type cast the above
    output data from the CSV parser.

12. However, when rendering type cast input data to CSV text, non-string types
    MUST be converted to a string in such a way that minimal information is
    lost.
      - Integers and floats MUST be rendered as a string version of themselves.
      - Booleans `true` and `false` MUST be rendered as `true` and `false`
        strings, not as `1` or `0` numbers. If numbers are used the resulting
        CSV data is indistinguishable from actual integer numbers.
      - `Null`/`nil` values MUST be rendered as empty strings.

13. When parsing input CSV data all forms of line breaks (CRLF, LF, and CR) MUST
    be supported.
14. When rendering output CSV data, CRLF MUST be used for line breaks to ensure
    maximum cross-platform compatibility.

License
-------

[CC0 1.0 Universal](http://creativecommons.org/publicdomain/zero/1.0/)

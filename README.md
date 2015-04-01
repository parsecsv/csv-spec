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

- **Field** — A singular String value within a record.
- **Record** (or **Row**) — A collection of fields. This is often referred to
  as a "line", but a single record can in span multiple text lines if a field
  within it contains one or more line breaks.
- **Delimiter** — The character used to separate fields withing a
  row. Commonly this will be a comma (`,`), but semi-colons (`;`) or tabs
  (`\t`) are two other popular delimiter characters.
- **Header** — The first row is often used to contain the column names for all
  remaining rows. Header names would be used as key names when CSV data is
  converted to JSON for example.
- **Line Break** — Line breaks in CSV files should be CRLF (`\r\n`).


## CSV Format Definition

- These rules are mostly based on the corresponding section from
  [RFC 4180][def], with minor changes, clarifications and improved examples.
- Where relevant, examples include both the CSV text version and the
  equivalent data in JSON format.
- Line breaks in the CSV examples are displayed using the `¬` character.

[def]: http://tools.ietf.org/html/rfc4180#section-2

### Rules

1.  Each record starts at the beginning of its own line, and ends with a line
    break (CRLF).

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

2.  Though it is recommended, the last record in a file is not required to
    have a ending line break.

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

3.  There may be an optional header line appearing as the first line of the
    file with the same format as normal records. This header will contain
    names corresponding to the fields in the file, and must contain the same
    number of fields as the records in the rest of the file.

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

4.  Within each record and the optional header, there may be one or more
    fields, separated by a delimiter (normally a comma). Each record should
    contain the same number of fields throughout the file.

    CSV (invalid):

    ```csv
    aaa,bbb,ccc¬
    111,222,333,444¬
    xxx,yyy,zzz¬
    ```

5.  The last field in the record must not be followed by a comma. This results
    in a additional field with nothing in it.

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

6.  Spaces are considered part of a field and should not be ignored. For
    example:

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

7.  Fields containing line breaks, double quotes, or the delimiter character
    (normally a comma) must be enclosed in double-quotes.

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

8.  A double-quote appearing inside a field must be escaped by preceding it
    with another double quote, and the field itself must be enclosed in double
    quotes.

    CSV:

    ```csv
    aaa,"b""bb",ccc¬
    ```

    JSON:

    ```json
    [ ["aaa", "b\"bb", "ccc"] ]
    ```

9.  Though it is not recommended, each field may be enclosed in double quotes
    even if it does not contain a line break, double quote, or delimiter
    character.

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

10. All fields are always strings. CSV itself does not support type casting to
    integers, floats, booleans, or anything else. It is not a CSV library's
    responsibility to type cast input CSV data.

    If type casting is required, it is be up to the developer using a specific
    CSV library to ensure types are correctly dealt with.

    Input JSON:

    ```json
    [ [10, true, 0.3, "aaa"],
      [11, false, 2.13, "bbb"] ]
    ```

    Output CSV:

    ```csv
    10,true,0.3,aaa¬
    11,false,2.13,bbb¬
    ```

    Output CSV parsed back to JSON:

    ```json
    [ ["10", "true", "0.3", "aaa"],
      ["11", "false", "2.13", "bbb"] ]
    ```

    At this point it is up to the developer themselves to type cast the above
    output data from the CSV parser.

11. However, when rendering type cast input data to CSV text, non-string
    types should be converted to a string in such a way that minimal
    information is lost.
      - Integers and floats should simply be rendered as a string version
        of themselves.
      - Booleans `true` and `false` should be rendered as `true` and `false`
        strings, not as `1` or `0` numbers. If numbers are used the resulting
        CSV data is indistinguishable from actual integer numbers.
      - Null/Nil values should be rendered as empty strings.


## License

[CC0 1.0 Universal](http://creativecommons.org/publicdomain/zero/1.0/)

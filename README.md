# Disclaimer
This page is a WORK IN PROGRESS. Any all things may change at a moments notice.

# Contributing
- Note there are many, many encoding types, a quick sample can be seen here: https://en.wikipedia.org/wiki/Binary-to-text_encoding#Encoding_standards
  - The table below includes the encoding that have been suggested in various discussions around the web, compared against various concerns also suggested around the web. 
  - If you have a suggestion for a new encoding: please open a PR adding the item to the table. If you are adding a 
  - If you have a suggestion for a new concern: please open a PR adding the item to the table after opening a discussion thread for linking.
  

# Summary of Concerns and Tradeoffs
- Many want alternate UUID encodings for different reasons.
  - In order to objectively evaluate options, this table has been made to help analyze which properties various solutions have and don't have. 
  - Not all concerns are equally important, but all concerns are important enough to be considered when weighing options.

- Where possible alt Encoding should attempt to use the starting example UUID, in Unsigned Integer or Binary from [RFC9562][RFC9562], Section 4
  - Figure 2: `11111000000111010100111110101110011111011110110000010001110100001010011101100101000000001010000011001001000111100110101111110110`
  - Figure 3: `329800735698586629295641978511506172918`
  - This ensures we have an accurate comparison of the output encoding from the same starting point.
 
| Encoding | Variant      | Example Encoding Output                | [Text Length (Max Chars)](#text-length-max-chars) | [Padding](#padding) | [Checksums](#checksums) | [Special Characters](#special-characters) | [Character Exclusions](#character-exclusions) | [Case Sensitive](#case-sensitive) | [Sorts the Same as Binary](#sorts-the-same-as-binary) | [URL Path](#url-path) | [URL Query Param](#url-query-param) | [DNS Record](#dns-record) | [Double Click to Copy](#double-click-to-copy) | [Supports 128+ bit Inputs](#supports-128-bit-inputs) | [Parse(uuid_text) Updatable](#parseuuid_text-updatable) | [Makes Everyone Happy](#makes-everyone-happy) | SPEC Reference             | Notes                                                                           |
| ---      | ---          |---                                     | ---                                               | ---                 | ---                     | ---                                       | ---                                           | ---                               | ---                                                   | ---                   | ---                                 | ---                       | ---                                           | ---                                                  | ---                                                     | ---                                           | ---                        | ---                                                                             |
| Base16   | Hex and Dash | `f81d4fae-7dec-11d0-a765-00a0c91e6bf6` | 36                                                | N                   | N                       | Y                                         | N                                             | N                                 | Y                                                     | Y                     | Y                                   | Y                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC9562]                  | Default String Format                                                           |
| Base16   | Base         | `f81d4fae7dec11d0a76500a0c91e6bf6`     | 32                                                | N                   | N                       | N                                         | N                                             | N                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [RFC9562]                  |                                                                                 |
| Base32   | Base         | `7AOU7LT55QI5BJ3FACQMSHTL6Y======`     | 32                                                | Y                   | N                       | N                                         | N                                             | N                                 | N                                                     | N                     | N                                   | Y                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648]         |                                                                                 |
| Base32   | Hex          | `V0EKVBJTTG8T19R502GCI7JBUO======`     | 32                                                | Y                   | N                       | N                                         | N                                             | N                                 | Y                                                     | Y                     | Y                                   | Y                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648]         |                                                                                 |
| Base32   | Crockford    | `Z0EMZBKXXG8X19V502GCJ7KBYR`           | 26                                                | N                   | Y                       | N                                         | Y                                             | N*                                | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [CrB32][CrB32]             | Encoding uppercase, decoding can use upper/lower.                               |
| Base32   | NCName       | `bzjv6jsglv4pkfkyaarninsfbl`           | 26                                                | Y                   | N                       | N                                         | Y                                             | N                                 | N                                                     | Y                     | Y                                   | Y                         | Y                                             | N*                                                   | Y                                                       | N                                             | [NCName][NCName]           | May support 128+?                                                               |
| Base32   | z-base       | `TODO`                                 | xx                                                | N                   | N                       | N                                         | Y                                             | N                                 | N                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [z-base-32][z-base-32]     |                                                                                 |
| Base32   | geohash      | `TODO`                                 | xx                                                | N                   | N                       | N                                         | Y                                             | N                                 | N                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [py-geohash][py-geohash]   | Trailing 0's removed?                                                           |
| Base36   | LiosK        | `TODO`                                 | xx                                                | N                   | N                       | N                                         | N                                             | N                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [LIOSK][LIOSK]             | Not sure if there is a better SPEC.                                             |
| Base48   | Fabio        | `TODO`                                 | xx                                                | N                   | N                       | N                                         | Y                                             | Y                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [fabiolimace][fabiolimace] | Not sure if there is a better SPEC.                                             |
| Base52   | Fabio        | `TODO`                                 | xx                                                | N                   | N                       | N                                         | Y                                             | Y                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [fabiolimace][fabiolimace] | Not sure if there is a better SPEC.                                             |
| Base58   | BTC          | `Xe22UfxT3rxcKJEAfL5373`               | 22                                                | N                   | Y                       | N                                         | Y                                             | Y                                 | Y                                                     | Y                     | Y                                   | N                         | Y                                             | Y                                                    | Y                                                       | N                                             | [BTC][BTC]                 |                                                                                 |
| Base62   | IEEE         | `7YBUWgZR1mKSqGyj9tVViw`               | xx                                                | Y                   | N                       | N                                         | N*                                            | Y                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [base62spec][base62spec]   | [base62wiki][base62wiki] cites IEEE doc. Excludes numeric because not required. |
| Base64   | Base         | `+B1Prn3sEdCnZQCgyR5r9g==`             | 24                                                | Y                   | N                       | Y                                         | N                                             | Y                                 | N                                                     | N                     | N                                   | N                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648]         |                                                                                 |
| Base64   | URL Safe     | `-B1Prn3sEdCnZQCgyR5r9g`               | 22                                                | Y                   | N                       | Y                                         | N                                             | Y                                 | N                                                     | Y                     | Y                                   | N                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648]         |                                                                                 |
| Base64   | NCName       | `BymvkyMuvHqKrAARahsihL`               | 22                                                | Y                   | N                       | Y                                         | N                                             | Y                                 | N                                                     | Y                     | Y                                   | Y                         | Y                                             | N*                                                   | Y                                                       | N                                             | [NCName][NCName]           | May support 128+?                                                               |
| PLACE    | HOLDER       | ``                                     | xx                                                | X                   | X                       | X                                         | X                                             | X                                 | X                                                     | X                     | X                                   | X                         | X                                             | X                                                    | X                                                       | N                                             | [][]                       | Placeholder for new Values                                                      |

---

### Number of New Alphabets 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/14)
- This is the number of alphabets or distinct encodings that have to be implemented to support the indicated solution.
- More alphabets are not necessarily bad, but consider this in relation to the work required by library maintainers that will have to update their libraries to support the new specification.

## Encoding Specific Items

### Text Length (Max Chars) 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/19)
- Indicates the maximum number of characters that could be output for a given UUID.
- To gleam potential the Maximum output of a given encoding use UUID MAX [RFC9562][RFC9562], Section 5.10 as the starting point:
  - Binary: `11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111`
  - Unsigned Integer: `340282366920938463463374607431768211455`

### Padding 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/20)
- Generally if padding is part of the output the maximum length of the output will be fixed
- Padding characters are usually `=` but may be different in different encoding languages
- Depending on the algorithm padding may be omitted from the output. This of course  will change the Text Length (Max Chars). 

### Checksums
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/27)
- Some encoding types can leverage checksum characters to help ensure encode/decode is working as expected.
- This character could be `=` same as the pad character or another entirely.
- This will also add length to the new encoding changing the Text Length (Max Chars).

### Special Characters
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/30)
- Does the encoding utilize special characters other than numbers/letters.
- Usually as the encoding base goes up, so does the items in the alphabet
- Note: This excludes padding/checksums which are discussed separately.

### Character Exclusions
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/31)
- Some libraries exclude specific alphabetic characters: `Oo`, `Ii` and `Ll`, sometimes `Uu`?
- These characters help human readability since `I`/`l` can be confused with `1` and `O` can be confused with `0` while the `U` character can create accidental obscenities in the encoding.
  - Also scenarios where "v==u" and "2==z" as seen in z-base-32
- While usually specific to the alphabetic characters, there may be gaps in the numeric characters available too.
- Note that qualifier is "Y" if an encoding goes out of their way to exclude a character. e.g Base32hex does not use the full A-Z alphabet because it is not required, not for some other specific purpose as noted above.

### Case Sensitive 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/21)
- Fore example: Does `A == a`?
- This impacts things like sortability.
- Generally, anything above Base32 includes Uppercase and Lowercase as distinct encoding characters.

### Sorts the Same as Binary 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/22)
- This indicates if values encoded with this encoding sort the same as the raw bytes they represent.
- This typically depends on the alphabet used, and not all alphabets have this property.
- Specifically, how are symbols/special characters treated? Do they sort first (before 0) or last (after Z)

## Misc App Considerations.

### URL Path 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/23)
- This indicates if a value can be used in the path part of a URL without requiring any [URL Percent Encoding][URL Percent Encoding].
- This specifically means values must not contain `/`, although other considerations may also apply.

### URL Query Param 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/23)
- This indicates if a value can be used in the query string of a URL without requiring any [URL Percent Encoding][URL Percent Encoding].
- This specifically means values must not contain any of `?=/+`, although other considerations may also apply.

### DNS Record 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/23)
- For an encoding to be compatible as a DNS record, it just be case insensitive and only contain certain characters.
  - ASCII: https://www.rfc-editor.org/rfc/rfc9499.html#section-2-1.30
  - Non-ASCII: https://www.rfc-editor.org/rfc/rfc9499.html#section-2-1.36 (and https://www.rfc-editor.org/rfc/rfc5892)
- Note that there's overlap here with other things that might typically be used in a DNS entry: names of hosts, virtual machines, containers, etc.

### HTML/XML
- HTML ID can't start with a number
- XML Namespaces cannot start with a letter or underscore (could use UUID URN instead)
- "Virtually every programming language likewise requires identifiers such as variables and function names to start with a letter or underscore, and very few admit hyphens." [NCName][NCName]

## Other Considerations

### Double Click to Copy 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/24)
- Do we need to "double click" to select the entire encoding or "triple click" to select the entire encoding?

### Supports 128+ bit Inputs 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/25)
- Indicates this encoding is free of any known conflicts from using an input value that is 128+ bits
- This takes into the account that, at some point, a UUID format of 128+ bits could exist and helps future proof the alternate encoding.

### Parse(uuid_text) Updatable 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/categories/general)
- This indicates if existing generic `Parse(uuid_text)` functions in UUID libraries can be updated in a reliable and unambiguous way to parse the new format.
- This specifically means that if this value is Y, then Parse can unambiguously determine just by looking at the input if it is the old UUID hex and dash string format, or the new input (including any and all variations of it).

### Makes Everyone Happy 
- [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/discussions/categories/general)
- This is of course a light-hearted reminder that nobody is going to get everything they want.
- The objective is to determine, to the extent possible, which path is most useful to the most number of important use cases to the most people.

---

[RFC9562]: https://www.rfc-editor.org/rfc/rfc9562.html
[RFC4648]: https://datatracker.ietf.org/doc/html/rfc4648
[CrB32]: https://www.crockford.com/base32.html
[URL Percent Encoding]: https://en.wikipedia.org/wiki/Percent-encoding
[BTC]: https://github.com/bitcoin/bitcoin/blob/master/src/base58.cpp
[NCName]: https://datatracker.ietf.org/doc/html/draft-taylor-uuid-ncname-00
[z-base-32]: https://philzimmermann.com/docs/human-oriented-base-32-encoding.txt
[py-geohash]: https://github.com/vinsci/geohash/blob/master/Geohash/geohash.py
[fabiolimace]: https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/issues/3#issuecomment-1101761083
[base62spec]: https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=4737287
[base62wiki]: https://en.wikipedia.org/wiki/Base62
[LIOSK]: https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/issues/3#issuecomment-1101761108

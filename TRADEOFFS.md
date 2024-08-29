# Disclaimer
This page is a WORK IN PROGRESS. Any all things may change at a moments notice.

# Summary of Concerns and Tradeoffs
- Many want alternate UUID encodings for different reasons.
- In order to objectively evaluate options, this table has been made to help analyze which properties various solutions have and don't have. 
- Not all concerns are equally important, but all concerns are important enough to be considered when weighing options.
- Where possible alt Encoding should attempt to use the starting example UUID, in Unsigned Integer or Binary from [RFC9562][RFC9562], Section 4
  - Figure 2: `11111000000111010100111110101110011111011110110000010001110100001010011101100101000000001010000011001001000111100110101111110110`
  - Figure 3: `329800735698586629295641978511506172918`
  - This ensures we have an accurate comparison of the output encoding from the same starting point.
 
| Encoding | Variant      | Example Encoding Output                | [Number of New Alphabets](#number-of-new-alphabets) | [Text Length (Max Chars)](#text-length-max-chars) | [Padding](#padding)  | [Case Sensitive](#case-sensitive) | [Sorts the Same as Binary](#sorts-the-same-as-binary) | [URL Path](#url-path) | [URL Query Param](#url-query-param) | [DNS Record](#dns-record) | [Double Click to Copy](#double-click-to-copy) | [Supports 128+ bit Inputs](#supports-128-bit-inputs) | [Parse(uuid_text) Updatable](#parseuuid_text-updatable) | [Makes Everyone Happy](#makes-everyone-happy) | SPEC Reference     | Notes                 |
| ---      | ---          |---                                     | ---                                                 | ---                                               | ---                  | ---                               | ---                                                   | ---                   | ---                                 | ---                       | ---                                           | ---                                                  | ---                                                     | ---                                           | ---                | ---                   |
| Base16   | Base         | `f81d4fae7dec11d0a76500a0c91e6bf6`     | 1                                                   | 32                                                | N                    | N                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [RFC9562]          |                       |
| Base16   | Hex and Dash | `f81d4fae-7dec-11d0-a765-00a0c91e6bf6` | 1                                                   | 36                                                | N                    | N                                 | Y                                                     | Y                     | Y                                   | Y                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC9562]          | Default String Format |
| Base64   | Base         | `+B1Prn3sEdCnZQCgyR5r9g==`             | 1                                                   | 24                                                | Y                    | Y                                 | N                                                     | N                     | N                                   | N                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648] | w/o URL Safe          |
| Base64   | URL Safe     | `-B1Prn3sEdCnZQCgyR5r9g`               | 1                                                   | 22                                                | Y                    | Y                                 | N                                                     | Y                     | Y                                   | N                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648] | w/ URL Safe           |
| Base58   | BTC          | `Xe22UfxT3rxcKJEAfL5373`               | 1                                                   | 22                                                | N                    | Y                                 | Y                                                     | Y                     | Y                                   | N                         | Y                                             | Y                                                    | Y                                                       | N                                             | [BTC][BTC]         | A used in BitCoin     |
| Base32   | Base         | `7AOU7LT55QI5BJ3FACQMSHTL6Y======`     | 1                                                   | 32                                                | Y                    | N                                 | N                                                     | N                     | N                                   | Y                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648] | w/o Extended Hex      |
| Base32   | URL Safe     | `V0EKVBJTTG8T19R502GCI7JBUO======`     | 1                                                   | 32                                                | Y                    | N                                 | Y                                                     | Y                     | Y                                   | Y                         | N                                             | Y                                                    | Y                                                       | N                                             | [RFC4648][RFC4648] | w/o Extended Hex      |
| Base32   | Crockford    | `Z0EMZBKXXG8X19V502GCJ7KBYR`           | 1                                                   | 26                                                | N                    | N*                                 | Y                                                     | Y                     | Y                                   | Y                         | Y                                             | Y                                                    | Y                                                       | N                                             | [CrB32][CrB32]     | W/o `OoIiLl`          |

```
* Encoding uppercase, decoding can use upper/lower.
```
---

### Number of New Alphabets
- This is the number of alphabets or distinct encodings that have to be implemented to support the indicated solution.
- More alphabets are not necessarily bad, but consider this in relation to the work required by library maintainers that will have to update their libraries to support the new specification.

### Text Length (Max Chars)
- Indicates the maximum number of characters that could be output for a given UUID.
- To gleam potential the Maximum output of a given encoding use UUID MAX [RFC9562][RFC9562], Section 5.10 as the starting point:
  - Binary: `11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111`
  - Unsigned Integer: `340282366920938463463374607431768211455`

### Padding
- Generally if padding is part of the output the maximum length of the output will be fixed
- Padding characters are usually `=` but may be different in different encoding languages
- Depending on the algorithm padding may be ommited from the output. This of course  will change the Text Length (Max Chars). 

### Case Sensitive
- Fore example: Does `A == a`?
- This impacts things like sortability.

### Sorts the Same as Binary
- This indicates if values encoded with this encoding sort the same as the raw bytes they represent.
- This typically depends on the alphabet used, and not all alphabets have this property.

### URL Path
- This indicates if a value can be used in the path part of a URL without requiring any [URL Percent Encoding][URL Percent Encoding].
- This specifically means values must not contain `/`, although other considerations may also apply.

### URL Query Param
- This indicates if a value can be used in the query string of a URL without requiring any [URL Percent Encoding][URL Percent Encoding].
- This specifically means values must not contain any of `?=/+`, although other considerations may also apply.

### DNS Record
- For an encoding to be compatible as a DNS record, it just be case insensitive and only contain certain characters.
  - ASCII: https://www.rfc-editor.org/rfc/rfc9499.html#section-2-1.30
  - Non-ASCII: https://www.rfc-editor.org/rfc/rfc9499.html#section-2-1.36 (and https://www.rfc-editor.org/rfc/rfc5892)
- Note that there's overlap here with other things that might typically be used in a DNS entry: names of hosts, virtual machines, containers, etc.

### Double Click to Copy
- Do we need to "double click" to select the entire encoding or "triple click" to select the entire encoding?

### Supports 128+ bit Inputs
- Indicates this encoding is free of any known conflicts from using an input value that is 128+ bits
- This takes into the account that, at some point, a UUID format of 128+ bits could exist and helps future proof the alternate encoding.

### Parse(uuid_text) Updatable
- This indicates if existing generic `Parse(uuid_text)` functions in UUID libraries can be updated in a reliable and unambiguous way to parse the new format.
- This specifically means that if this value is Y, then Parse can unambiguously determine just by looking at the input if it is the old UUID hex and dash string format, or the new input (including any and all variations of it).

### Makes Everyone Happy
- This is of course a light-hearted reminder that nobody is going to get everything they want.
- The objective is to determine, to the extent possible, which path is most useful to the most number of important use cases to the most people.

---

[RFC9562]: https://www.rfc-editor.org/rfc/rfc9562.html
[RFC4648]: https://datatracker.ietf.org/doc/html/rfc4648
[CrB32]: https://www.crockford.com/base32.html
[URL Percent Encoding]: https://en.wikipedia.org/wiki/Percent-encoding
[BTC]: https://github.com/bitcoin/bitcoin/blob/master/src/base58.cpp

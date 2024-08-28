# Summary of Concerns and Tradeoffs

WORK IN PROGRESS

Many people want alternate UUID encodings for different reasons. In order to objectively evaluate options, this table has been made to help analyze which properties various solutions have and don't have.  Not all concerns are equally important, but all concerns are important enough to be considered when weighing options.

| Concern | B64 | B58 | B32 | CrB32 | Multi | Multi+Marker |
| --- | --- | --- | --- | --- | --- | --- |
| Number of New Alphabets | 1 | 1 | 1 | 1 | ? | ? |
| Text Length | 21 | 21 | 26 | 26 | ? | ? |
| Parse(uuid_text) Can Be Reliably Updated | Y |  Y |  Y |  Y | N | Y |
| Sorts the Same as Binary | N | Y | N | Y | ? | ? |
| URL Path | N | Y | Y | Y | ? | ? |
| URL Query Param | Y | Y | Y | Y | ? | ? |
| DNS Record | N | N | Y | Y | ? | ? |
| Supports Long UUID | Y | Y | Y | Y | Y | Y |
| Makes Everyone Happy | N | N | N | N | N | N |

## Concerns

Elaborate on each concern here:

### Number of New Alphabets

This is the number of alphabets or distinct encodings that have to be implemented to support the indicated solution.  More alphabets are not necessarily bad, but consider this in relation to the work required by library maintainers that will have to update their libraries to support the new specification.

### Parse(uuid_text) Can Be Reliably Updated

This indicates if existing Parse(uuid_text) functions in UUID libraries can be updated in a reliable and unambiguous way to parse the new format.  This specifically means that if this value is Y, then Parse can unambiguously determine just by looking at the input if it is the old UUID hex format, or the new input (including any and all variations of it).

### Sorts the Same as Binary

This indicates if values encoded with this encoding sort the same as the raw bytes they represent.  This typically depends on the alphabet used, and not all alphabets have this property.

### URL Path

This indicates if a value can be used in the path part of a URL without requiring any encoding.  This specifically means values must not contain `/`, although other considerations may also apply.

### URL Query Param

This indicates if a value can be used in the query string of a URL without requiring any encoding.  This specifically means values must not contain any of `?=/+`, although other considerations may also apply.

### DNS Record

For an encoding to be compatible as a DNS record, it just be case insensitive and only contain certain characters.  TODO: find the exact list, but typically safe values are from `A` to `Z`, `0` to `9` and `-`.

Note that there's overlap here with other things that might typically be used in a DNS entry: names of hosts, virtual machines, containers, etc.

### Supports Long UUID

Indicates this encoding is free of any known conflicts from using it with UUIDs of alternate lengths (TODO: link to separate proposal on that).

### Makes Everyone Happy

This is of course a light-hearted reminder that nobody is going to get everything they want.

The objective is to determine, to the extent possible, which path is most useful to the most number of important use cases to the most people.

## Encodings

Clarify encodings here:

### B64

Example: `6iviGd15PaLlRV81f07-0F`

Base64 as specified in RFC4648.

No padding.

This encoding is case sensitive.

This encoding does not sort the same as its underlying binary equivalent.

### B58

Example: `2xWev4eTWKU6qbpYoevyi`

Base58 as used in BitCoin (TODO: need spec/reference)

This encoding is case sensitive.

This encoding sorts the same as its underlying binary equivalent.

### B32

Example: `068sdnb1a1tbqe7xsksgygvefo`

Base32 as specified in RFC4648.

No padding.

This encoding is case insensitive.

This encoding does not sort the same as its underlying binary equivalent.

### CrBR32

Example: `068sdnb1a1tbqe7xsksgygvef0`

Crockford Base 32: https://www.crockford.com/base32.html . With or without the alternate decode symbols `OoIiLl` (TBD if allowing these is worth the added decoder complexity).

No padding.

This encoding is case insensitive.

This encoding sorts the same as its underlying binary equivalent.

### Multi

Example: `2xWev4eTWKU6qbpYoevyi` OR `068sdnb1a1tbqe7xsksgygvef0`

This is a placeholder encompassing proposals that want to allow multiple encodings without a reliable way to tell the difference between them.

TODO: If someone wants something like this, add the exact spec as a separate entry.

### Multi+Marker

Example: `2xWev4eTWKU6qbpYoevyi:base58`

This is a placeholder encompassing proposals that want to allow multiple encodings with an indication encoded into the value of which encoding was used.

TODO: If someone wants something like this, add the exact spec as a separate entry.

---
title: Alternate UUID Encoding Methods
abbrev: alternate-uuid-encoding
category: std

docname: draft-davis-uuidrev-alternate-uuid-encoding-methods
submissiontype: IETF
updates: '9562'
date: 2025
consensus: true
v: 3
area: ART
workgroup: uuidrev
keyword:
  - uuid
  - encoding
  - base02
  - base10
  - base16
  - base32
  - base36
  - base52
  - base58
  - base64
  - base85
pi: [toc, sortrefs, symrefs]
venue:
  group: "Revise Universally Unique Identifier Definitions (uuidrev)"
  type: Working Group
  mail: uuidrev@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/uuidrev/
  github: uuid6/new-uuid-encoding-techniques-ietf-draft
  latest: https://example.com/LATEST

author:
  - name: Kyzer R. Davis
    email: kydavis@cisco.com
    org: Cisco Systems
  - initials: BGP.
    name: Brad G. Peabody
    email: brad@peabody.io

normative:
  RFC9562:
  RFC4648:

informative:
  RFC20:
  RFC3986:
  RFC9499:
  XML:
    target: https://www.w3.org/TR/2009/REC-xml-names-20091208/
    title: Namespaces in XML 1.0 (Third Edition)
    author:
    - org: W3C
    date: 2009-12
  HTML:
    target: https://html.spec.whatwg.org/
    title: HTML Living Standard
    author:
    - org: whatwg
    date: 2025-11
  CSS:
    target: https://www.w3.org/TR/CSS2/syndata.html#value-def-identifier
    title: Cascading Style Sheets Level 2 Revision 1 (CSS 2.1) Specification
    author:
    - org: W3C
    date: 2011-06
  NCNAME:
    target: https://datatracker.ietf.org/doc/draft-taylor-uuid-ncname/
    title: Compact UUIDs for Constrained Grammars
    author:
    - name: Dorian Taylor
    date: 2025-09
  DCB32:
    target: https://www.crockford.com/base32.html
    title: Douglas Crockford's Base32
    author:
    - name: Douglas Crockford
    date: 2002-11
  ZB32:
    target: https://philzimmermann.com/docs/human-oriented-base-32-encoding.txt
    title: human-oriented base-32 encoding
    author:
    - name: Zooko O'Whielacronx
    date: 2009-11
  GEOHASH:
    target: https://github.com/vinsci/geohash/blob/master/Geohash/geohash.py
    title: Geohash
    author:
    - org: Geohash
    date: 2009-11
    seriesinfo:
      commit: 3f0ce0b
  IEEEBase62:
    target: https://ieeexplore.ieee.org/document/4737287
    title: A secure, lossless, and compressed Base62 encoding
    author:
    - org: IEEE
    date: 2008-11
  Base64sort:
    target: https://github.com/kyzer-davis/base64-sort-ietf-draft
    title: A Sortable Base64 Alphabet
    author:
    - name: Kyzer Davis
    date: 2025-12
  Bitcoin:
    target: https://github.com/bitcoin/bitcoin/blob/master/src/base58.cpp
    title: A secure, lossless, and compressed Base62 encoding
    author:
    - org: Bitcoin
    date: 2008-11
    seriesinfo:
      commit: fae71d3
  Flickr:
    target: https://www.flickr.com/groups/api/discuss/72157616713786392/
    title: manufacturing flic.kr style photo URLs
    author:
    - name: Kellen
  Base58xrp:
    target: https://xrpl.org/docs/references/protocol/data-types/base58-encodings
    title: base58 Encodings
    author:
    - org: XRP Ledger
    date: 2024
  Z85:
    target: https://rfc.zeromq.org/spec/32/
    title: 32/Z85
    author:
    - org: iMatix Corporation
    date: 2013

--- abstract

This document presents considerations and best practices for alternate Universally Unique Identifier (UUID) encoding methods observed in the industry.

This document updates {{RFC9562}} to provide suggested alternate encoding methods, best practices and the various implementation considerations required for choosing the correct encoding for an application.

When selected correctly; these alternate UUID encodings perform better on the wire, in a database, or within various other application logics versus the unnecessarily verbose text representation from {{RFC9562}}.

--- middle

# Introduction {#introduction}

The original "hex-and-dash" (8-4-4-4-12) format of UUIDs defined in {{RFC9562}} represents a 128-bit UUID value as a 288-bit text value.
Although this format is useful for human readability, it is verbose for storage, transmission, and application parsing.

Because of this, developers have long used alternate UUID encodings.
Many bespoke implementations—either within a single application or as one-off libraries—address shortcomings of the hex-and-dash form.
However, most applications, databases, and standard libraries expose UUIDs using the formats described in IETF documents.
If a feature is not specified by a widely used standard, implementers typically do not provide it and users do not see it.
Consequently, although alternate encodings have been used and reimplemented for many years, there is no single applicable standard and implementations are inconsistent.

During the revision process that produced UUIDv6, UUIDv7, and UUIDv8 for {{RFC9562}}, alternate encodings were a major topic of discussion.
The level of interest showed that a separate document focused on alternate encodings was needed.

This document describes the most common alternate UUID encoding methods observed in the field and discusses the advantages and disadvantages of each.
The latter half of the document presents best practices and considerations implementations should weigh when selecting, implementing, or defining new alternate UUID encodings not covered in {{alt_encodings}}{: format="title"}.
These best practices are based on years of research, community feedback, and inspection of over 55 alternate-encoding implementations across 17 alphabets (see {{examples}}{: format="title"}).

# Terminology

## Conventions and Definitions {#conventions_definitions}

{::boilerplate bcp14-tagged}

## Abbreviations {#abbreviations}

The following abbreviations are used in this document:

{: indent="14"}
UUID
: Universally Unique Identifier {{RFC9562}}

BaseXX
: Base where XX references two numeric values with any leading 0 kept.
For example Base02, Base10, Base16, Base32, Base64, etc.

# Alternate UUID Encoding Methods {#alt_encodings}

{{RFC9562, Section 4}} details that at its core, any given UUID is a 128 bit value which can be represented as Base02 (binary), Base10 (decimal/integer), base16 (hex) and even a custom "hex-and-dash" string format; which is Base16 (hex) with a few extra dash characters added to create a unique UUID string format that is instantly recognizable.
Further, the section goes on to discuss other string formats that often use the Hex and Dash format or Integer format.

While these are the "well-known" UUID formats, a UUID is fundamentally a 128-bit value and can be represented using many BaseXX alphabets.

The large number of possible BaseXX alphabets, together with the considerations described in {{best_practices}}, makes it impractical to cover every alphabet variant in a single document.
Therefore, this document focuses on BaseXX alphabets that use US-ASCII ({{RFC20}}) and that are most commonly used in real-world implementations or were prototyped during the draft's preparation.

UUID library implementers SHOULD consider adding support for at least one alternate encoding described here and MAY support additional encodings, including ones not listed.
If you implement a BaseXX alphabet not covered in the sections below, consult the considerations in {{best_practices}}{: format="title"}.

For a quick comparison of alphabets, see {{alphabetTable}}.

| Encoding | Variant                | Alphabet Order                                                                            |
| Base16   | {{RFC9562, Section 4}} | `0123456789ABCDEF` with dash `-` characters added                                              |
| Base16   | {{RFC4648, Section 8}} | `0123456789ABCDEF`                                                                        |
| Base32   | {{RFC4648, Section 6}} | `ABCDEFGHIJKLMNOPQRSTUVWXYZ234567`                                                        |
| Base32   | {{RFC4648, Section 7}} | `0123456789ABCDEFGHIJKLMNOPQRSTUV`                                                        |
| Base32   | {{DCB32}}              | `0123456789ABCDEFGHJKMNPQRSTVWXYZ`                                                        |
| Base36   | ---                    | `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ`                                                    |
| Base52   | ---                    | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz`                                    |
| Base58   | {{Bitcoin}}            | `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz`                              |
| Base62   | {{IEEEBase62}}         | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789`                          |
| Base64   | {{RFC4648, Section 4}} | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/`                        |
| Base64   | {{RFC4648, Section 5}} | `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_`                        |
| Base64   | {{Base64sort}}         | `-0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz`                        |
| Base85   | {{Z85}}                | `0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ.-:+=^!/*?&<>()[]{}@%$#`   |
{: #alphabetTable title='Alt UUID Encoding Alphabet Comparison'}

Note: All UUID examples in this document are alternate encodings of {{RFC9562, Section 4}}'s Example UUID starting from either Figure 2 (binary) or Figure 3 (integer).
Each section has a few example encodings; any UUID featured in RFC9562 can be found in the {{test_vectors}} table converted to the corresponding BaseXX alphabet encoding.

## Base32 {#Base32}
Base32 alphabets generally use 5 bits per character, producing a 26-character output when encoding a 128-bit UUID; padding may be present.
These alphabets include standards-based Base32 ({{RFC4648, Section 6}}), Base32hex ({{RFC4648, Section 7}}), Douglas Crockford's Base32 {{DCB32}}, Z-Base-32 {{ZB32}}, and {{GEOHASH}}.

Base32 alphabets vary the most in character choice because implementers can be selective about which characters are included.
{{best_practice_exclusions}}{: format="title"} and alphabet ordering (see {{best_practice_sorting}}{: format="title"}) are important considerations and differ across implementations.

Base32 is often case-insensitive: lowercase letters are frequently treated the same as uppercase because the alphabet does not require distinct upper- and lower-case characters.
{{best_practice_special}}{: format="title"} are rarely used with Base32, except for {{best_practice_padding}}{: format="title"} or {{best_practice_checksums}}{: format="title"} features, which can often be omitted.

Base32 is a strong choice when available. Base32hex ({{RFC4648}}) and Crockford's Base32 ({{DCB32}}) are recommended options for a 26-character UUID that works well for both machines and humans.
See {{sampleBase32HexUUID}} and {{sampleBase32Crockford}} for examples.

Z-Base-32 {{ZB32}}{: format="title"} makes many changes to the underlying alphabet to accommodate a specific use case and is not recommended for UUIDs.
Although {{GEOHASH}} is closer to Base32hex and excludes some characters like Crockford's alphabet, its removal of trailing zeros is an unusual behavior not used by other BaseXX alphabets and may make adoption difficult.

There are many other Base32 variants; consult {{best_practices}}{: format="title"} when selecting an alphabet not listed here.

~~~
V0EKVBJTTG8T19R502GCI7JBUO
~~~
{: #sampleBase32HexUUID title='Example UUID encoded as Base32 Hex'}

~~~
Z0EMZBKXXG8X19V502GCJ7KBYR
~~~
{: #sampleBase32Crockford title='Example UUID encoded as Douglas Crockford's Base32'}

## Base36 {#Base36}
Base36 alphabets are similar to Base32 but use about 5.1699 bits per character, reducing a 128-bit UUID to 25 characters.
Base36 typically uses digits `0–9` followed by uppercase `A–Z`, which affects case-sensitivity considerations (see {{best_practice_sensitivity}}{: format="title"}).
Alphabet ordering matters: using an alphabet with ordered as `A–Z0–9` would change sort order and is therefore uncommon.

Base36 also compresses nullish characters (see {(best_practice_compression}}) which may be desirable to further reduce the size of the encoded UUID.

Base36 is reasonably available ({{best_practice_availability}}) but generally offers smaller benefits compared with base32hex or Crockford's Base32, so it is not recommended as a required encoding for all UUID implementations.

See {{sampleBase36}} for an example.

~~~
EOSWZOLG3BSX0ZN8OTQ1P8OOM
~~~
{: #sampleBase36 title='Example UUID encoded as Base36'}

## Base52 {#Base52}
Base52 uses the 52 alphabetic characters `A–Z` and `a–z`, producing a 23-character UUID at about 5.700 bits per character.

Although not widely used, Base52 has useful properties: it contains no special characters ({{best_practice_special}}{: format="title"}) and excludes digits, avoiding issues with leading digits in some contexts.
Its ordering can be chosen to preserve desirable sort behavior (see {{best_practice_sorting}}{: format="title"}).

Base52 also compresses nullish characters (see {(best_practice_compression}}) which may be desirable to further reduce the size of the encoded UUID.

See {{sampleBase52}} for an example.

~~~
FraqvVvqUBoEOFXsPYOPwUm
~~~
{: #sampleBase52 title='Example UUID encoded as Base52'}

## Base58 {#Base58}
Base58, used by {{Bitcoin}} (and {{Flickr}}), is a popular alphabet similar to Crockford's Base32 in its character exclusions.
Base58 encodes a 128-bit UUID into a 22-character string (about 5.857 bits per character).
Base58 typically omits {{best_practice_padding}}{: format="title"}, and most implementations follow the same alphabet, with the {{Base58xrp}} variant being a notable exception.

Base58 also compresses nullish characters (see {(best_practice_compression}}) which may be desirable to further reduce the size of the encoded UUID.

Base58 {{best_practice_availability}}{: format="title"} varies, but the format is well-known and commonly used for UUIDs in practice.

See {{sampleBase58}} for an example.

~~~
B7wc88dU4e3NyJEj3e944DK
~~~
{: #sampleBase58 title='Example UUID encoded as Bitcoin's Base58'}

## Base62 {#Base62}
{{IEEEBase62}} (defined by the IEEE) is the largest BaseXX alphabet that contains no special characters ({{best_practice_special}}{: format="title"}).
Base62 encodes a 128-bit UUID as a 22-character string (about 5.954 bits per character).
During research the authors observed few libraries or discussions specifically using Base62 for UUIDs.

Base62 also compresses nullish characters (see {(best_practice_compression}}) which may be desirable to further reduce the size of the encoded UUID.

See {{sampleBase62}} for an example.

~~~
HiLegqjbBwUc0Q8tJ3ffs6
~~~
{: #sampleBase62 title='Example UUID encoded as IEEE's Base62'}

## Base64 {#Base64}
Base64 alphabets require special characters ({{best_practice_special}}{: format="title"}) to complete the 64-symbol set, so they typically do not use character exclusions.
Base64 encodes a 128-bit UUID into a 22-character string (6 bits per character), similar in length to Base58 and Base62.

Because symbols are involved, many other permutations exist and this document cannot cover them all; see {{best_practice_usage}}{: format="title"} when selecting an alphabet.
The most common variants are the Base64 alphabet in {{RFC4648, Section 4}} and Base64url in {{RFC4648, Section 5}}.

Base64 is recommended when maximum compactness is desired without {(best_practice_compression}}{: format="title"}. For most use cases, Base64url ({{RFC4648, Section 5}}) is encouraged because it is safe for URLs, filenames and {{best_practice_applications}}{: format="title"} use cases.
When lexicographical sorting that matches binary order is critical (for example, with UUIDv6 or UUIDv7), consider `Base64sort` ({{Base64sort}}).

UUID implementers SHOULD implement Base64url because it already enjoys widespread availability ({{best_practice_availability}}{: format="title"}).

See {{sampleBase64}} and {{sampleBase64url}} for examples.

~~~
+B1Prn3sEdCnZQCgyR5r9g==
~~~
{: #sampleBase64 title='Example UUID encoded as Base64'}

~~~
-B1Prn3sEdCnZQCgyR5r9g==
~~~
{: #sampleBase64url title='Example UUID encoded as Base64 URL and Filename safe'}

## Base85 {#Base85}
Base85 encodes 4 bytes as 5 characters (about 6.409 bits per character), so a 128-bit UUID can be represented in 20 characters.
These alphabets include many special characters ({{best_practice_special}}{: format="title"),} and provide the most compact textual representation among the alphabets discussed.
Common variants include Z85, Base85, and ASCII85.

Some Base85 variants include compression techniques for certain byte sequences (see {{best_practice_compression}}{: format="title"}). Those techniques are not universal across all Base85 alphabets.

Z85 is the recommended Base85 variant for UUIDs when Base85 is desired.

See {{sampleBase85}} for an example.

~~~
{-iekEE4M)R!2>3:Stl>
~~~
{: #sampleBase85 title='Example UUID encoded as Base85 using Z85 alphabet'}

# UUID Encoding Best Practices {#best_practices}

There are several factors to consider when choosing an encoding for a UUID.
Requirements that matter for one application may be irrelevant for another; the authors have grouped the most important considerations into best-practice categories in the sections that follow.

Each section focuses on UUID-specific concerns, but many of the topics generalize to selecting alternate encodings for other data.

## Usage {#best_practice_usage}

Understanding how a UUID will be used is the most important step when choosing an alternate encoding.
That analysis affects many of the considerations that follow: there is no single correct choice for every scenario.

For example, a UUID used as a logging prefix should be compact to reduce log size, while a UUID that must be read or spoken by humans should avoid characters that are easily confused.
Shorter encodings with character exclusions are preferable when values are recited by phone or written by hand to reduce transcription errors.

Datastores (see {{RFC9562}}) care about case sensitivity, alphabet ordering, and encoded length because these factors affect sorting, storage, and performance at scale.
UUIDs transmitted between machines benefit from compact encodings to reduce bytes on the wire and improve behavior on limited-MTU or high-latency networks.

For resource-constrained devices such as IoT endpoints, prefer encodings that are computationally inexpensive to encode and decode.

## Maximum Character Length {#best_practice_length}

The number of characters in a given encoding alphabet is directly correlated to the size of the alternate UUID encoding output.

As a starting point the encodings defined by RFC9562 are Binary, Integer, and Hex.
Base02 (Binary) version of a UUID is 128 characters in length consisting of 0s and 1s.
The Base10 (decimal/integer) version of a UUID is 39 characters consisting of characters 0 through 9 while the Base16 (Hex) version of a UUID shortens this to 32 characters by using characters 0 through 9 and A through F.

Base32 libraries tend to produce a UUID output of 26 characters in length and base64 drops the output UUID to 22 characters.

With this trend one may be tempted to simply pick the highest BaseXX encoding available and get the smallest encoding output possible however as the output encoding decreases, the base alphabet increases.
At a specific point both numeric values, upper and lowercase values may be present and any number of unique symbols can be present in the base alphabet.
The addition and ordering of these can have rippling effects that one must properly consider.
Further, some of these alphabets require unique math to properly apply to the fixed-length 128 bit UUID which may increase computational complexity in unfavorable ways along with padding to output a similarly fixed-length value.

Simply picking the biggest base alphabet to get the smallest encoding only works if the only consideration an implementation cares about is the actual size of the output UUID.

To glean the maximum size of a given BaseXX encoding with a 128-bit UUID, convert the binary form of UUID MAX from {{RFC9562, Section 5.10}}.

{{lengthTable}} details the maximum length of various BaseXX Encoding methods with UUID MAX, without padding present.

| Encoding | Variant                | Maximum Character Length |
| Base16   | {{RFC9562, Section 4}} | 36                       |
| Base16   | {{RFC4648, Section 8}} | 32                       |
| Base32   | {{RFC4648, Section 6}} | 26                       |
| Base32   | {{RFC4648, Section 7}} | 26                       |
| Base32   | {{DCB32}}              | 26                       |
| Base36   | ---                    | 25                       |
| Base52   | ---                    | 23                       |
| Base58   | {{Bitcoin}}            | 22                       |
| Base62   | {{IEEEBase62}}         | 22                       |
| Base64   | {{RFC4648, Section 4}} | 22                       |
| Base64   | {{RFC4648, Section 5}} | 22                       |
| Base64   | {{Base64sort}}         | 22                       |
| Base85   | {{Z85}}                | 20                       |
{: #lengthTable title='Alt UUID Encoding Length Comparison'}

## Padding Characters {#best_practice_padding}

Padding characters go hand in hand with the previous section involving the maximum character length of an output alternate UUID encoding.

The most common padding character is the equal sign (=) however it could theoretically be any character possible.
It is recommended to use the equal sign since it is the most well-known padding character among the various BaseXX encodings.

Further, this character is almost always in the least significant, right-most section of a given alternate UUID encoding.
The padding SHOULD stay in this position and moving the character can have ramifications for sorting.

Since UUIDs defined by {{RFC9562}} are always 128 bits, padding MAY be omitted and the resulting output will always be the same length.

{{paddingTable}} compares padding usage among the BaseXX encoding methods in this document.

| Encoding | Variant                | Padding |
| Base16   | {{RFC9562, Section 4}} | N       |
| Base16   | {{RFC4648, Section 8}} | N       |
| Base32   | {{RFC4648, Section 6}} | Y (=)   |
| Base32   | {{RFC4648, Section 7}} | Y (=)   |
| Base32   | {{DCB32}}              | N       |
| Base36   | ---                    | N       |
| Base52   | ---                    | N       |
| Base58   | {{Bitcoin}}            | N       |
| Base62   | {{IEEEBase62}}         | Y       |
| Base64   | {{RFC4648, Section 4}} | Y (=)   |
| Base64   | {{RFC4648, Section 5}} | Y (=)   |
| Base64   | {{Base64sort}}         | Y (=)   |
| Base85   | {{Z85}}                | N       |
{: #paddingTable title='Alt UUID Encoding Padding Comparison'}

## Checksum Characters {#best_practice_checksums}

Some newer BaseXX encodings include checksum characters that are used to ensure the input/output encoding and decoding is working as expected.

The actual character and algorithm used vary among BaseXX implementations.

Including checksums increases both the maximum size of the output encoding and the computation requirements for encoding/decoding alternate UUID formats.

For alternate UUIDs transmitted among two machines it may be beneficial to include a checksum character to validate the given UUID has not been modified during transport before using it for various operations thus increasing the resiliency of the alternate UUID generation and parsing process.

For applications that simply care about generating UUIDs but do not need to parse, checksums MAY be omitted entirely.

{{checksumTable}} compares checksum usage among the BaseXX encoding methods in this document.

| Encoding | Variant                | Checksums         |
| Base16   | {{RFC9562, Section 4}} | N                 |
| Base16   | {{RFC4648, Section 8}} | N                 |
| Base32   | {{RFC4648, Section 6}} | N                 |
| Base32   | {{RFC4648, Section 7}} | N                 |
| Base32   | {{DCB32}}              | Y (*,~,$,=,U,u)   |
| Base36   | ---                    | N                 |
| Base52   | ---                    | N                 |
| Base58   | {{Bitcoin}}            | Y (SHA256-based)  |
| Base62   | {{IEEEBase62}}         | N                 |
| Base64   | {{RFC4648, Section 4}} | N                 |
| Base64   | {{RFC4648, Section 5}} | N                 |
| Base64   | {{Base64sort}}         | N                 |
| Base85   | {{Z85}}                | N                 |
{: #checksumTable title='Alt UUID Encoding Checksum Comparison'}

## Special Characters {#best_practice_special}

After all of the alpha-numeric characters are used a BaseXX alphabet must resort to using special characters such as but not limited to underscore (_), hyphen (-), plus (+), forward slash (/), dollar sign ($), etc.
This usually occurs around 62 characters (ten digits 0-9, 26 lowercase english characters, 26 uppercase english characters) but may happen with earlier BaseXX alphabets when specific characters are excluded.

The inclusion of these characters decrease the overall size of the encoded UUID but have implications in regards to not just sorting and readability but often application usage.
For example Base64 as defined by {{RFC4648}} has two alphabets which change the special characters to ensure safe usage with URLs and Filenames.
Another example is the ability to double-click a UUID and copy the value.
When special characters are present the text highlight starts and stops between these characters.
In such a scenario, if this is important one should implement a BaseXX Alphabet with no such characters.

When evaluating a baseXX encoding, care must be taken to ensure the special characters are compatible with the desired use case.

{{specialTable}} compares special character inclusions among the BaseXX encoding methods in this document.

| Encoding | Variant                | Special Characters                  |
| Base16   | {{RFC9562, Section 4}} | Y (dash)                            |
| Base16   | {{RFC4648, Section 8}} | N                                   |
| Base32   | {{RFC4648, Section 6}} | N                                   |
| Base32   | {{RFC4648, Section 7}} | N                                   |
| Base32   | {{DCB32}}              | N                                   |
| Base36   | ---                    | N                                   |
| Base52   | ---                    | N                                   |
| Base58   | {{Bitcoin}}            | N                                   |
| Base62   | {{IEEEBase62}}         | N                                   |
| Base64   | {{RFC4648, Section 4}} | Y (plus, forward-slash)             |
| Base64   | {{RFC4648, Section 5}} | Y (dash, underscore)                |
| Base64   | {{Base64sort}}         | Y (dash, underscore)                |
| Base85   | {{Z85}}                | Y (numerous, see {{alphabetTable}}) |
{: #specialTable title='Alt UUID Encoding Special Character Comparison'}

## Character Exclusions {#best_practice_exclusions}

Newer BaseXX alphabet may exclude characters and keep only one value present.

This often occurs when multiple characters are similar in shape which which may lead to issues when humans are required to read the characters aloud or manually transpose the characters by hand.
Some characters may be excluded for other reasons.
For example it may be advantageous to remove 0 as a possible option to eliminate the possibility of leading 0s in the output data which may lead to problems in specific use cases where leading 0s are not permitted or may be inadvertently removed by the application.
Finally, in some smaller BaseXX alphabets it is possible to exclude characters because they are not required to fill out the space.
The notable example is Base32hex defined by {{RFC4648, Section 7}} which only uses A through V because the remaining slots are filled by numeric 0-9.

The following character groupings illustrate some of the problematic characters that are similarly shaped.

~~~
- 0, O, o
- 1, I, i, L, l
- 2, Z, z
- 5, S, s
- V, v, U, u
~~~

Unfortunately there is no consistent method for which character is omitted or removed.
Some BaseXX alphabets may remove the numeric characters while others may remove one or both of the english characters often replacing them with symbols in larger BaseXX alphabets to fill the gaps.

Implementations should vet BaseXX alphabets with character exclusions thoroughly to ensure any possible inclusion of symbols does not cause problems while also verifying that sorting is not impacted if sorting is a requirement.

{{exclusionTable}} compares character exclusions among the BaseXX encoding methods in this document.

| Encoding | Variant                | Character Exclusions |
| Base16   | {{RFC9562, Section 4}} | N                    |
| Base16   | {{RFC4648, Section 8}} | N                    |
| Base32   | {{RFC4648, Section 6}} | N                    |
| Base32   | {{RFC4648, Section 7}} | N                    |
| Base32   | {{DCB32}}              | Y (Ii,Ll,Oo,Uu)      |
| Base36   | ---                    | N                    |
| Base52   | ---                    | N                    |
| Base58   | {{Bitcoin}}            | Y (0,I,O,l)          |
| Base62   | {{IEEEBase62}}         | N                    |
| Base64   | {{RFC4648, Section 4}} | N                    |
| Base64   | {{RFC4648, Section 5}} | N                    |
| Base64   | {{Base64sort}}         | N                    |
| Base85   | {{Z85}}                | N                    |
{: #exclusionTable title='Alt UUID Encoding Character Exclusion Comparison'}

## Case Sensitivity {#best_practice_sensitivity}

Case sensitivity becomes important when the alphabet includes more than 32 characters.
For alphabets at or below Base32 (for example, Base16), uppercase and lowercase characters are often treated interchangeably; for larger alphabets, uppercase and lowercase letters represent distinct values.

In most scenarios, uppercase letters are ordered before lowercase letters. This ordering directly impacts sorting because many applications sort uppercase characters before lowercase characters.
Sorting is further covered in {{best_practice_sorting}}{: format="title"}.

The BaseXX alphabet used also changes comparison logic: "aBc" does not necessarily equal "AbC" in all encodings.

{{sensitivityTable}} compares case sensitivity among the BaseXX encoding methods in this document.

| Encoding | Variant                | Case Sensitive |
| Base16   | {{RFC9562, Section 4}} | N              |
| Base16   | {{RFC4648, Section 8}} | N              |
| Base32   | {{RFC4648, Section 6}} | N              |
| Base32   | {{RFC4648, Section 7}} | N              |
| Base32   | {{DCB32}}              | N              |
| Base36   | ---                    | N              |
| Base52   | ---                    | Y              |
| Base58   | {{Bitcoin}}            | Y              |
| Base62   | {{IEEEBase62}}         | Y              |
| Base64   | {{RFC4648, Section 4}} | Y              |
| Base64   | {{RFC4648, Section 5}} | Y              |
| Base64   | {{Base64sort}}         | Y              |
| Base85   | {{Z85}}                | Y              |
{: #sensitivityTable title='Alt UUID Encoding Case Sensitivity Comparison'}

## Sorting and Ordering {#best_practice_sorting}

Lexicographical sorting of UUIDs is a very important factor to many applications and the ordering of the BaseXX alphabet directly impacts the sorting of the encoded UUID output such as those created by UUIDv6 and UUIDv7 where Lexicographically sortable UUIDs are a key feature of underlying algorithm.

The BaseXX alphabets generally consist of two or more of the following 4 components with optional omissions as defined by {{best_practice_exclusions}}:

~~~
1. Numeric Characters: 0-9
2. Uppercase Character: A-Z
3. Lowercase Characters: a-z
4. Symbols: underscore (_), hyphen (-), plus (+), forward slash (/), dollar sign ($), etc.
~~~

The ordering of these is traditionally Numeric, Uppercase, Lowercase and Symbols. However for ordering purposes, some symbols may be placed at a different position to adhere to their position found in US-ASCII ({{RFC20}}).

A common sorting example is a symbol like the dash (-) character may sort before numbers while other special such as an underscore (_) will be sorted after the uppercase characters but before the lowercase characters if sorted via their ASCII values.

If an implementation would like to ensure the sort order of the encoded value remains the same as the Base02 (binary) or Base10 (decimal) value one should scrutinize the position of the underlying BaseXX alphabets, their ordering and the includes symbols.

{{alphabetTrend}} illustrate the ordering trends for some common BaseXX Alphabets.

~~~
- Base10: 0123456789
- Base36: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
- Base62: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
- Base64: ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_
~~~
{: #alphabetTrend title='Alphabet Ordering Comparison'}

{{sortTable}} compares binary sorting among the BaseXX encoding methods in this document.

| Encoding | Variant                | Sorts the Same as Binary |
| Base16   | {{RFC9562, Section 4}} | Y                        |
| Base16   | {{RFC4648, Section 8}} | Y                        |
| Base32   | {{RFC4648, Section 6}} | N                        |
| Base32   | {{RFC4648, Section 7}} | Y                        |
| Base32   | {{DCB32}}              | Y                        |
| Base36   | ---                    | Y                        |
| Base52   | ---                    | Y                        |
| Base58   | {{Bitcoin}}            | Y                        |
| Base62   | {{IEEEBase62}}         | Y                        |
| Base64   | {{RFC4648, Section 4}} | N                        |
| Base64   | {{RFC4648, Section 5}} | N                        |
| Base64   | {{Base64sort}}         | Y                        |
| Base85   | {{Z85}}                | N                        |
{: #sortTable title='Alt UUID Encoding Sorting Comparison'}

## Compressing Nullish Characters {#best_practice_compression}

Some BaseXX alphabets may use varying techniques for compressing nullish characters which can sometimes yield values smaller than what is cited in {{best_practice_length}}.

{{Base36}}{: format="title"}, {{Base52}}{: format="title"}, {{Base58}}{: format="title"}, {{Base62}}{: format="title"} will trim leading zero characters before attempting to encode the data.

{{Base85}}{: format="title"} alphabets may employ a technique to compress a continuous four byte sequence of ASCII Space characters as the character lowercase y.

{{Base85}}{: format="title"} alphabets may also compress a continuous four byte sequence of ASCII Zero characters as the character lowercase z.

The leading zero compression technique can be observed in {{test_vectors_nil_uuid}} using NIL UUID input to illustrate the output featuring a much smaller value than the outputs found in other test vectors.

This variability may be desirable to produce even smaller UUIDs however this could also prove a problem if an application expects a UUID of a specific fixed-length value found in {{best_practice_length}}.

## Encoding Availability {#best_practice_availability}

The BaseXX alphabet encoding availability may be the second greatest hurdle implementations navigate as they chose a viable alternate encoding for UUID.

For example, traditional Base64url safe sees far more implementations than Base32hex while even fewer have implemented Base62 or even Crockford's Base32 variant.

The ability to utilize a given encoding or provide a customized alphabet may be a limiting factor in implementations.
In this scenario implementors will need to write their own, leverage third-party libraries or select a viable alternative.

## Computation {#best_practice_computation}

The various base encodings have more or less computational requirements based on the size of the alphabet, the total number of bits encoded per character, if checksums are involved, and if floating point math is required.

For reference Base02 uses 2 bits per character encoding while Base10 uses 4 bits per character and Base32 uses 5 bits and Base64 uses 6 bits.
While non–power-of-two alphabets like Base36, Base58, or Base62 use approximately 5.169, 5.857, and 5.954 bits per character respectively, the computation is log<sub>2</sub>(XX) where XX is the alphabet length.

{{bitsTable}} compares bits per character among the BaseXX encoding methods in this document.

| Encoding | Variant                | Bits per Character   |
| Base16   | {{RFC9562, Section 4}} | 4.000                |
| Base16   | {{RFC4648, Section 8}} | 4.000                |
| Base32   | {{RFC4648, Section 6}} | 5.000                |
| Base32   | {{RFC4648, Section 7}} | 5.000                |
| Base32   | {{DCB32}}              | 5.000                |
| Base36   | ---                    | 5.169                |
| Base52   | ---                    | 5.700                |
| Base58   | {{Bitcoin}}            | 5.857                |
| Base62   | {{IEEEBase62}}         | 5.954                |
| Base64   | {{RFC4648, Section 4}} | 6.000                |
| Base64   | {{RFC4648, Section 5}} | 6.000                |
| Base64   | {{Base64sort}}         | 6.000                |
| Base85   | {{Z85}}                | 6.409                |
{: #bitsTable title='Alt UUID Encoding Bits Comparison'}

## Application Specific {#best_practice_applications}

Some applications have very specific restrictions which may influence the alternate UUID encoding selection.
This section features a limited list compiling some known restrictions that implementations may consider while working with alternate UUID encodings.

### URI, URL, URN {#best_practice_uri_url_urn}

Uniform Resource Identifiers (URIs) have a specific list of reserved characters defined by {{RFC3986, Section 2.2}} which require percent encoding to be used.

It is advisable to avoid BaseXX alphabets that include these symbols.

### DNS Record {#best_practice_dns}

Domain Name Systems (DNS) are case insensitive with the period or dot character (.) being a reserved symbol. {{RFC9499, Section 2}}

Thus BaseXX alphabets that utilize this character or use upper and lowercase values as defined by {#best_practice_sensitivity}{: format="title"} should be avoided.

### XML, HTML, CSS {#best_practice_markup}

Extensible Markup Language ({{XML}}) namespaces cannot start with a leading numeric character.

While older versions of Hypertext Markup Language ({{HTML}}}) had restrictions on element identifiers starting with a leading digit; HTML5 does not have any such restrictions.
The same goes for Cascading Style Sheet ({{CSS}}) selectors identifiers where older versions would have issues when identifiers start with digits.

Thus for XML, HTML, and CSS it is advantageous to use some advanced methods such as:

{: spacing="compact"}

- Leveraging the alternate UUID encoding technique defined in {{NCNAME}} to ensure that the first digit is always an uppercase alphabet character.
- Utilize {{Base52}} which ensures the output UUID does not start with a special character or digit.
- Simply prefix the first non-digit/non-special character in the given alphabet.
For example, if uppercase A is available; prefix this character to satisfy the constraints.

# Parsing {#parsing}

This document focuses on generating UUIDs via alternate formats over parsing UUIDs of alternate encoding formats.
The generic default for any existing `uuid_parse(uuid)` function SHOULD remain that of {{RFC9562, Section 4}} hex-and-dash string format.

Implementors MAY provide parse functionality to parse UUIDs of alternate formats such as `uuid_parse(uuid, encoding="base64url")`.
The distribution (and naming) of the encoding algorithm among disparate systems is outside of the scope of this document.

In practice this is seldom an issue because most implementations that need to parse UUIDs are aware of the encoding format used by their peers.
For example, the sender and receiver of a Base64url-encoded UUID are often within the same application boundary.
It is uncommon for two different systems to exchange UUIDs in a way that requires the receiver to parse an alternate-format UUID back into binary; typically the receiver treats the received UUID as an opaque string.

# Recommendations {#recommendations}

{{recommendationTable}} below provides an at-a-glance set of recommendations for each BaseXX alphabet described in this document for new implementations looking to introduce alternate encodings with UUID.

While some rows state "Not Recommended", if a library is currently providing this alternate encoding; they may continue to do so without deprecating, removing or otherwise undoing existing functionality.

| Encoding | Variant                | Recommendation |
| Base32   | {{RFC4648, Section 6}} | Not Recommended with UUID. Leverage Base32hex or Crockford's Base32. |
| Base32   | {{RFC4648, Section 7}} | Recommended when sorting is required. |
| Base32   | {{DCB32}}              | Recommended when sorting and Humans usage is required. |
| Base36   | ---                    | Not Recommended with UUID. Leverage Base32hex or Crockford's Base32. |
| Base52   | ---                    | Recommended when sorting is required, special characters/digits pose problems variable length UUIDs is not a problem. |
| Base58   | {{Bitcoin}}            | Recommended when sorting, humans usage is required and variable length UUIDs is not a problem. |
| Base62   | {{IEEEBase62}}         | Recommended when variable length UUIDs is not a problem. |
| Base64   | {{RFC4648, Section 4}} | Not Recommended with UUID. Leverage Base64url or Base64sort. |
| Base64   | {{RFC4648, Section 5}} | Recommended for most use cases. |
| Base64   | {{Base64sort}}         | Recommended when sorting is required. |
| Base85   | {{Z85}}                | Recommended when symbols are of no issues. |
{: #recommendationTable title='General Guidance for Alternate UUID Encoding Recommendations'}

# Security Considerations {#security_considerations}

Section {{best_practice_checksums}}{: format="title"} addresses the primary security-related concern in this document: data-integrity validation for UUIDs transmitted over the wire.

No additional security considerations are identified.

# IANA Considerations {#iana_considerations}

This document has no IANA actions.

--- back

# Acknowledgments {#acknowledgements}

The authors gratefully acknowledge the contributions of

{: spacing="compact"}

- Yulian Kuncheff for their work on the UUID Formatter test tool which was valuable in comparing various UUID encoding formats side-by-side.
- LiosK for early prototyping of various BaseXX Alphabets
- Sergey Prokhorenko for continuously ensuring we work on this document.

As well as all of those in the IETF community and on GitHub to who contributed to the discussions which resulted in this document.

# Changelog {#changelog}

{:removeInRfc}

draft-00:

{: spacing="compact"}

- Initial Release

# Test Vectors {#test_vectors}

The test vectors in the upcoming sections map the UUIDs found in RFC9562 Sections to their appropriate BaseXX Alphabet Encoding without padding.

All of the BaseXX ALphabets described int his document and others not described can be viewed at the online tool the Author's are using for prototyping and comparison: https://uuidformattester.yuli.dev/  (Source Code: https://github.com/daegalus/uuid-format-tester)

## Generic UUID

| Encoding | Variant                | RFC9562, Section 4                     |
| Base16   | {{RFC9562, Section 4}} | `f81d4fae-7dec-11d0-a765-00a0c91e6bf6` |
| Base16   | {{RFC4648, Section 8}} | `f81d4fae7dec11d0a76500a0c91e6bf6`     |
| Base32   | {{RFC4648, Section 6}} | `7AOU7LT55QI5BJ3FACQMSHTL6Y`           |
| Base32   | {{RFC4648, Section 7}} | `V0EKVBJTTG8T19R502GCI7JBUO`           |
| Base32   | {{DCB32}}              | `Z0EMZBKXXG8X19V502GCJ7KBYR`           |
| Base36   | ---                    | `EOSWZOLG3BSX0ZN8OTQ1P8OOM`            |
| Base52   | ---                    | `FraqvVvqUBoEOFXsPYOPwUm`              |
| Base58   | {{Bitcoin}}            | `Xe22UfxT3rxcKJEAfL5373`               |
| Base62   | {{IEEEBase62}}         | `HiLegqjbBwUc0Q8tJ3ffs6`               |
| Base64   | {{RFC4648, Section 4}} | `+B1Prn3sEdCnZQCgyR5r9g`               |
| Base64   | {{RFC4648, Section 5}} | `-B1Prn3sEdCnZQCgyR5r9g`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `{-iekEE4M)R!2>3:Stl>`                 |
{: #exampleUUID title='UUID Test Vectors for RFC9562, Section 4'}

## NIL UUID  {#test_vectors_nil_uuid}

| Encoding | Variant                | RFC9562, Section 5.9                   |
| Base16   | {{RFC9562, Section 4}} | `00000000-0000-0000-0000-000000000000` |
| Base16   | {{RFC4648, Section 8}} | `00000000000000000000000000000000`     |
| Base32   | {{RFC4648, Section 6}} | `AAAAAAAAAAAAAAAAAAAAAAAAAA`           |
| Base32   | {{RFC4648, Section 7}} | `00000000000000000000000000`           |
| Base32   | {{DCB32}}              | `00000000000000000000000000`           |
| Base36   | ---                    | `0`                                    |
| Base52   | ---                    | `A`                                    |
| Base58   | {{Bitcoin}}            | `1`                                    |
| Base62   | {{IEEEBase62}}         | `0`                                    |
| Base64   | {{RFC4648, Section 4}} | `AAAAAAAAAAAAAAAAAAAAAA`               |
| Base64   | {{RFC4648, Section 5}} | `AAAAAAAAAAAAAAAAAAAAAA`               |
| Base64   | {{Base64sort}}         | `----------------------`               |
| Base85   | {{Z85}}                | `00000000000000000000`                 |
{: #exampleUUIDvNIL title='UUID Test Vectors for RFC9562, Section 5.9: Nil UUID'}

## MAX UUID  {#test_vectors_max_uuid}

| Encoding | Variant                | RFC9562, Section 5.10                  |
| Base16   | {{RFC9562, Section 4}} | `FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF` |
| Base16   | {{RFC4648, Section 8}} | `FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF`     |
| Base32   | {{RFC4648, Section 6}} | `77777777777777777777777774`           |
| Base32   | {{RFC4648, Section 7}} | `VVVVVVVVVVVVVVVVVVVVVVVVVS`           |
| Base32   | {{DCB32}}              | `ZZZZZZZZZZZZZZZZZZZZZZZZZW`           |
| Base36   | ---                    | `p777777777777777777777777p`           |
| Base52   | ---                    | `GBIWTpZqojFGQPQPXtvbJAv`              |
| Base58   | {{Bitcoin}}            | `P8AQGAut7N92awznwCnjuQP`              |
| Base62   | {{IEEEBase62}}         | `HxECNQWFdpvuJxIw3HPrmH`               |
| Base64   | {{RFC4648, Section 4}} | `/////////////////////w`               |
| Base64   | {{RFC4648, Section 5}} | `_____________________w`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `s8W-!s8W-!s8W-!s8W-!`                 |
{: #exampleUUIDvMAX title='UUID Test Vectors for RFC9562, Section 5.10: MAX UUID'}

## UUIDv1 {#test_vectors_uuidv1}

| Encoding | Variant                | RFC9562, Section A.1                   |
| Base16   | {{RFC9562, Section 4}} | `C232AB00-9414-11EC-B3C8-9F6BDECED846` |
| Base16   | {{RFC4648, Section 8}} | `C232AB00941411ECB3C89F6BDECED846`     |
| Base32   | {{RFC4648, Section 6}} | `YIZKWAEUCQI6ZM6IT5V55TWYIY`           |
| Base32   | {{RFC4648, Section 7}} | `O8PAM04K2G8UPCU8JTLTTJMO8O`           |
| Base32   | {{DCB32}}              | `R8SAP04M2G8YSCY8KXNXXKPR8R`           |
| Base36   | ---                    | `BHW3F9QSZLQPYH8GTZ35HZ4UE`            |
| Base52   | ---                    | `EddHArfCMSZGGUJvWdXrHHq`              |
| Base58   | {{Bitcoin}}            | `Qys2KsgsAKw9ZKupo76FCh`               |
| Base62   | {{IEEEBase62}}         | `F4bpVC8trxK13yapr3UFrY`               |
| Base64   | {{RFC4648, Section 4}} | `wjKrAJQUEeyzyJ9r3s7YRg`               |
| Base64   | {{RFC4648, Section 5}} | `wjKrAJQUEeyzyJ9r3s7YRg`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `.znd(LOs.@V=F+O?P<+y`                 |
{: #exampleUUIDv1 title='UUID Test Vectors for RFC9562, Section A.1: UUIDv1'}

## UUIDv3 {#test_vectors_uuidv3}

| Encoding | Variant                | RFC9562, Section A.2                   |
| Base16   | {{RFC9562, Section 4}} | `5df41881-3aed-3515-88a7-2f4a814cf09e` |
| Base16   | {{RFC4648, Section 8}} | `5df418813aed351588a72f4a814cf09e`     |
| Base32   | {{RFC4648, Section 6}} | `LX2BRAJ25U2RLCFHF5FICTHQTY`           |
| Base32   | {{RFC4648, Section 7}} | `BNQ1H09QTKQHB2575T582J7GJO`           |
| Base32   | {{DCB32}}              | `BQT1H09TXMTHB2575X582K7GKR`           |
| Base36   | ---                    | `5K8PHACEYCDEGBDB1VWP0EAHQ`            |
| Base52   | ---                    | `CKwZBXIbBzTOnGTcpOSLRtq`              |
| Base58   | {{Bitcoin}}            | `CbuPE286MB6RsDazcU7sUy`               |
| Base62   | {{IEEEBase62}}         | `C1Rz9EB7xwwtaBE3hssbl8`               |
| Base64   | {{RFC4648, Section 4}} | `XfQYgTrtNRWIpy9KgUzwng`               |
| Base64   | {{RFC4648, Section 5}} | `XfQYgTrtNRWIpy9KgUzwng`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `?1\tb3ped>Lo2muJP>R)`                 |
{: #exampleUUIDv3 title='UUID Test Vectors for RFC9562, Section A.2: UUIDv3'}

## UUIDv4 {#test_vectors_uuidv4}

| Encoding | Variant                | RFC9562, Section A.3                   |
| Base16   | {{RFC9562, Section 4}} | `919108f7-52d1-4320-9bac-f847db4148a8` |
| Base16   | {{RFC4648, Section 8}} | `919108f752d143209bacf847db4148a8`     |
| Base32   | {{RFC4648, Section 6}} | `SGIQR52S2FBSBG5M7BD5WQKIVA`           |
| Base32   | {{RFC4648, Section 7}} | `I68GHTQIQ51I16TCV13TMGA8L0`           |
| Base32   | {{DCB32}}              | `J68GHXTJT51J16XCZ13XPGA8N0`           |
| Base36   | ---                    | `8M8SCPFUGFIJ4QENJ0IG77QG8`            |
| Base52   | ---                    | `DWDhSoKPZRoqkgBWaJcOckY`              |
| Base58   | {{Bitcoin}}            | `JyZVoFVQxQNmw2bsgr7D1R`               |
| Base62   | {{IEEEBase62}}         | `EaqKcHGnV4iQSYo57bXn6o`               |
| Base64   | {{RFC4648, Section 4}} | `kZEI91LRQyCbrPhH20FIqA`               |
| Base64   | {{RFC4648, Section 5}} | `kZEI91LRQyCbrPhH20FIqA`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `K=Y}zqQE&OO2(xP*D!xe`                 |
{: #exampleUUIDv4 title='UUID Test Vectors for RFC9562, Section A.3: UUIDv4'}

## UUIDv5 {#test_vectors_uuidv5}

| Encoding | Variant                | RFC9562, Section A.4                   |
| Base16   | {{RFC9562, Section 4}} | `2ed6657d-e927-568b-95e1-2665a8aea6a2` |
| Base16   | {{RFC4648, Section 8}} | `2ed6657de927568b95e12665a8aea6a2`     |
| Base32   | {{RFC4648, Section 6}} | `F3LGK7PJE5LIXFPBEZS2RLVGUI`           |
| Base32   | {{RFC4648, Section 7}} | `5RB6AVF94TB8N5F14PIQHBL6K8`           |
| Base32   | {{DCB32}}              | `5VB6AZF94XB8Q5F14SJTHBN6M8`           |
| Base36   | ---                    | `2RTO2O5WTBFMSYWZX7KHQ1Z8I`            |
| Base52   | ---                    | `BFPTsrUJhwfXFHQeVflYshu`              |
| Base58   | {{Bitcoin}}            | `6nTLogGvw2vmQjtATLqvLq`               |
| Base62   | {{IEEEBase62}}         | `BaXm0PEMkU5w7EKQaysNQO`               |
| Base64   | {{RFC4648, Section 4}} | `LtZlfeknVouV4SZlqK6mog`               |
| Base64   | {{RFC4648, Section 5}} | `LtZlfeknVouV4SZlqK6mog`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `f4KPZ>{GI&MeK63Sii1(`                 |
{: #exampleUUIDv5 title='UUID Test Vectors for RFC9562, Section A.4: UUIDv5'}

## UUIDv6 {#test_vectors_uuidv6}

| Encoding | Variant                | RFC9562, Section A.5                   |
| Base16   | {{RFC9562, Section 4}} | `1EC9414C-232A-6B00-B3C8-9F6BDECED846` |
| Base16   | {{RFC4648, Section 8}} | `1EC9414C232A6B00B3C89F6BDECED846`     |
| Base32   | {{RFC4648, Section 6}} | `D3EUCTBDFJVQBM6IT5V55TWYIY`           |
| Base32   | {{RFC4648, Section 7}} | `3R4K2J1359LG1CU8JTLTTJMO8O`           |
| Base32   | {{DCB32}}              | `3V4M2K1359NG1CY8KXNXXKPR8R`           |
| Base36   | ---                    | `1TM3WVVTP7XVNZXA2TV43IJWM`            |
| Base52   | ---                    | `liRiaXmgJTfBQppyCovyGu`               |
| Base58   | {{Bitcoin}}            | `4oVbpzb8BpnTH1mg11dmWd`               |
| Base62   | {{IEEEBase62}}         | `6FuGgfRpa7N6YbrlxT6FQ`                |
| Base64   | {{RFC4648, Section 4}} | `HslBTCMqawCzyJ9r3s7YRg`               |
| Base64   | {{RFC4648, Section 5}} | `HslBTCMqawCzyJ9r3s7YRg`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `9)3YwbpWEeV=F+O?P<+y`                 |
{: #exampleUUIDv6 title='UUID Test Vectors for RFC9562, Section A.5: UUIDv6'}

## UUIDv7 {#test_vectors_uuidv7}

| Encoding | Variant                | RFC9562, Section A.6                   |
| Base16   | {{RFC9562, Section 4}} | `017F22E2-79B0-7CC3-98C4-DC0C0C07398F` |
| Base16   | {{RFC4648, Section 8}} | `017F22E279B07CC398C4DC0C0C07398F`     |
| Base32   | {{RFC4648, Section 6}} | `AF7SFYTZWB6MHGGE3QGAYBZZR4`           |
| Base32   | {{RFC4648, Section 7}} | `05VI5OJPM1UC7664RG60O1PPHS`           |
| Base32   | {{DCB32}}              | `05ZJ5RKSP1YC7664VG60R1SSHW`           |
| Base36   | ---                    | `36TWI214QWJ7MGSVQ83NM8WF`             |
| Base52   | ---                    | `BrKaFlCsyjaiCYuzuWvtun`               |
| Base58   | {{Bitcoin}}            | `BihbxwwQ4NZZpKRH9JDCz`                |
| Base62   | {{IEEEBase62}}         | `CzFyajyRd5A9oiF8QCBUD`                |
| Base64   | {{RFC4648, Section 4}} | `AX8i4nmwfMOYxNwMDAc5jw`               |
| Base64   | {{RFC4648, Section 5}} | `AX8i4nmwfMOYxNwMDAc5jw`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `0E(rMD9zXlN8E+*3<O!N`                 |
{: #exampleUUIDv7 title='UUID Test Vectors for RFC9562, Section A.6: UUIDv7'}

## UUIDv8 {#test_vectors_uuidv8}

| Encoding | Variant                | RFC9562, Section B.1                   |
| Base16   | {{RFC9562, Section 4}} | `2489E9AD-2EE2-8E00-8EC9-32D5F69181C0` |
| Base16   | {{RFC4648, Section 8}} | `2489E9AD2EE28E008EC932D5F69181C0`     |
| Base32   | {{RFC4648, Section 6}} | `ESE6TLJO4KHABDWJGLK7NEMBYA`           |
| Base32   | {{RFC4648, Section 7}} | `4I4UJB9ESA7013M96BAVD4C1O0`           |
| Base32   | {{DCB32}}              | `4J4YKB9EWA7013P96BAZD4C1R0`           |
| Base36   | ---                    | `25VHD0YEN79F79OO0TV0BAE9S`            |
| Base52   | ---                    | `skNtHBdXlnQCRPhYqjxSkQ`               |
| Base58   | {{Bitcoin}}            | `5WhDz2zW6g9mHu7EP9hoVq`               |
| Base62   | {{IEEEBase62}}         | `BG6ufXDWs4d4Dd5uoJIAi0`               |
| Base64   | {{RFC4648, Section 4}} | `JInprS7ijgCOyTLV9pGBwA`               |
| Base64   | {{RFC4648, Section 5}} | `JInprS7ijgCOyTLV9pGBwA`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `b-g=/f5?(>J(:6b{k%{w`                 |
{: #exampleUUIDv8time title='UUID Test Vectors for RFC9562, Section B.1: UUIDv8'}

| Encoding | Variant                | RFC9562, Section B.2                   |
| Base16   | {{RFC9562, Section 4}} | `5c146b14-3c52-8afd-938a-375d0df1fbf6` |
| Base16   | {{RFC4648, Section 8}} | `5c146b143c528afd938a375d0df1fbf6`     |
| Base32   | {{RFC4648, Section 6}} | `LQKGWFB4KKFP3E4KG5OQ34P36Y`           |
| Base32   | {{RFC4648, Section 7}} | `BGA6M51SAA5FR4SA6TEGRSFRUO`           |
| Base32   | {{DCB32}}              | `BGA6P51WAA5FV4WA6XEGVWFVYR`           |
| Base36   | ---                    | `5G8XXKU1AQGT02ZJGR8PA3EUU`            |
| Base52   | ---                    | `CIhPGFswaUyeIiUVKFQScIW`              |
| Base58   | {{Bitcoin}}            | `CNV2iY4mKiTS1uw8RxapEH`               |
| Base62   | {{IEEEBase62}}         | `CxumvfWqhqp4Kq4BkCGR1s`               |
| Base64   | {{RFC4648, Section 4}} | `XBRrFDxSiv2TijddDfH79g`               |
| Base64   | {{RFC4648, Section 5}} | `XBRrFDxSiv2TijddDfH79g`               |
| Base64   | {{Base64sort}}         | `TODO`                                 |
| Base85   | {{Z85}}                | `tOH@/jw}7nLzRq84E%t?`                 |
{: #exampleUUIDv8name title='UUID Test Vectors for RFC9562, Section B.2: UUIDv8'}

# Example UUID Base Encoding Tools, Libraries, and resources {#examples}

The following list of libraries, tools, code, packages and other resources is for illustrative and research purposes.

Neither the authors or IETF endorse any of these libraries or guarantee their contents safe and harmless.

Install, download or use this software at your own risk.

{:removeInRfc}

## Base32, Base

~~~
- https://github.com/chilts/sid
- https://www.jsdelivr.com/package/npm/uuid-encoder
- https://github.com/jetify-com/typeid
- https://docs.crunchybridge.com/api-concepts/eid
- https://hackage.haskell.org/package/ron-0.12/docs/RON-UUID.html
- https://help.sap.com/doc/abapdocu_751_index_htm/7.51/en-US/abencl_system_uuid.htm
~~~

## Base32, Hex

~~~
- https://github.com/rs/xid
~~~

## Base32, Crockford

~~~
- https://github.com/ulid/spec
- https://codeberg.org/prettyid/python
- https://ptrchm.com/posts/based-uuid/
- https://github.com/martinheidegger/uuid-b32
- https://docs.rs/fast32/latest/fast32/
- https://uuid.ramsey.dev/en/stable/rfc4122/version7.html
- https://crates.io/crates/crockford-uuid
- https://rymc.io/blog/2024/uuidv7-typeids-in-diesel/
- https://docs.rs/rusty_ulid/latest/rusty_ulid/
~~~

## Base32, NCNAME

~~~
- https://www.rubydoc.info/gems/uuid-ncname
~~~

## Base36

~~~
- https://github.com/paralleldrive/cuid2
- https://www.jsdelivr.com/package/npm/uuid-encoder
- https://duncan99.wordpress.com/2013/01/22/converting-uuid-to-base36/
- https://github.com/salieri/uuid-encoder
- https://stackoverflow.com/questions/62059588/shortening-a-guid
- https://gist.github.com/fabiolimace/508dd2dd9d32fd493b31a5f386d5d4bc
- https://classic.yarnpkg.com/en/package/base36-uuid
~~~

## Base58

~~~
- https://github.com/cbschuld/uuid-base58
- https://www.linkedin.com/pulse/advantages-using-base58-unique-identifier-databases-lucian-ivanov
- https://github.com/AlexanderMatveev/go-uuid-base58
- https://packagist.org/packages/cbschuld/php-uuid-base58
- https://classic.yarnpkg.com/en/package/uuid58
- https://blog.schochastics.net/posts/2024-08-24_short-uuids/
- https://www.jsdelivr.com/package/npm/uuid-encoder
~~~

## Base62

~~~
- https://github.com/boundary/flake
- https://github.com/segmentio/ksuid
- https://www.jsdelivr.com/package/npm/uuid-encoder
~~~

## Base64

~~~
- https://github.com/elastic/elasticsearch/blob/main/server/src/main/java/org/elasticsearch/common/UUIDs.java#L23
- https://github.com/chilts/sid
- https://github.com/twitter-archive/snowflake
- https://github.com/ppearcy/elasticflake/blob/master/src/main/java/org/limberware/elasticflake/Base64.java
- https://www.mongodb.com/docs/manual/reference/method/ObjectId.createFromBase64/
- https://firebase.blog/posts/2015/02/the-2120-ways-to-ensure-unique_68
- https://www.jsdelivr.com/package/npm/uuid-encoder
- https://base64-uuid.com/
- https://rcfed.com/Utilities/Base64GUID
- https://toolslick.com/conversion/data/guid
- https://guidgenerator.com/
- https://help.sap.com/doc/abapdocu_751_index_htm/7.51/en-US/abencl_system_uuid.htm
~~~

## Base85

~~~
- https://stackoverflow.com/a/772984
- http://codehardblog.azurewebsites.net/encoding-uuid-based-keys-to-save-memory/
- https://gist.github.com/Higgs1/fee62d230bd87257e0b0
- https://www.npmjs.com/package/pure-uuid
- https://cjhaas.com/2013/11/12/php-base85-encode-128-bit-integer-guiduuid/
- https://webpowered.tools/textcodec/
~~~

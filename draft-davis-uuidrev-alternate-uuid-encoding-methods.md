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
  - base58
  - base64
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
  Base62:
    target: https://ieeexplore.ieee.org/document/4737287
    title: A secure, lossless, and compressed Base62 encoding
    author:
    - org: IEEE
    date: 2008-11
    target: https://github.com/sergeyprokhorenko/Base64UUID
    title: Base64 UUID
    author:
    - name: Sergey Prokhorenko
    date: 2025-07
    seriesinfo:
      commit: 778a6a4

--- abstract

This document presents considerations and best practices for alternate Universally Unique Identifier (UUID) encoding methods found in the industry. Many of these methods facilitate greater efficiency in storage or transport versus the unnecessarily verbose text representation from {{RFC9562}}.

This document updates {{RFC9562}} to provide suggested alternate encoding methods, best practices and the various implementation considerations required for choosing the correct encoding for our application.

When selected correctly; these alternate UUID encodings perform better on the wire, in a database, or within various other application logics.

--- middle

# Introduction {#introduction}

The original "hex and dash", 8-4-4-4-12, format of UUIDs defined by {{RFC9562}} represents a 128 bit UUID value as a 288 bit value.
While this may be great for human readability; the only available UUID text representation suffers poorly for storage, transmission and application parsing.

Due to this shortcoming savvy programmers have been leveraging alternate encoding UUIDs for as long as UUIDs have been around. Bespoke implementations within a single application or a myriad of one-off libraries attempt to cover where the original encoding falls short. While these solve the problem in a bubble: most applications, databases, or standard libraries provide UUIDs as a built-in set of functionality which mirror the IETF documents. If a feature is not defined by an applicable specification documents, the implementors and maintainers do not write the code and thus the end users do not see the feature. Thus while the problem of alternate encodings has been known, and solved, for many years; there is no applicable standard document thus no consistent implementation across UUID implementations.

While overhauling RFC4122 content and creating UUIDv6, UUIDv7, and UUIDv8 for RFC9562; one of the largest topics for discussion was that of alternate encodings. This tremendous show of support for the topic has proven that an update is required. This topic grew in length to the point where it needed to be split out of the revision and into a separate document which focuses solely on the topic of alternate UUID Encoding.

This document will cover the most common alternate UUID Encoding methods used in the field while covering the pros and cons for each. The latter half of this document will cover the best practice and considerations that implementations should take into considerations while selecting, implementing and even building new alternate UUID encoding method outside of the items covered in {{alt_encodings}}. These best practices are a result of many years of research, community feedback and inspection of over 55 different alternate UUID encoding implementations across 17 different alphabets. (See {{examples}}.)

# Terminology

## Conventions and Definitions {#conventions_definitions}

{::boilerplate bcp14-tagged}

## Abbreviations {#abbreviations}

The following abbreviations are used in this document:

{: indent="14"}
UUID
: Universally Unique Identifier {{RFC9562}}

BaseXX
: Base where XX references two numeric values with any leading 0 kept. For example Base02, Base10, Base16, Base32, Base64, etc.

# Alternate UUID Encoding Methods {#alt_encodings}

{{RFC9562, Section 4}} details that at its core any given UUID is a 128 bit value which can be represented as Base02 (binary), base10 (integer), base16 (hex) and even a custom "hex-and-dash" string format which is Base16 (hex) with a few extra dash characters added to create a unique UUID string format that is instantly recognizable. Further, the section goes on to discuss other string formats that often use the Hex and Dash format or Integer format.

While these are the "well known" UUID formats. The truth is that since UUID is a single 128 bit data value. It can be easily transposed and represented by many common BaseXX alphabets.

The sheer number combinations of BaseXX Alphabets coupled with the various considerations found in {{best_practices}} make it impossible to cover every single BaseXX Alphabet combination in a single document. Instead this document will focus on the BaseXX Alphabets that see the most usage in real world implementations.

UUID standard library implementors SHOULD consider adding support for at least one alternate encoding defined in this document and MAY provide support for more including those not listed in this document. Refer to the various considerations found in {{best_practices}} if creating or implementing a BaseXX Alphabet for UUID that is not covered already in the following sections.

Note: All UUID examples in this document are alternate encodings of {{RFC9562, Section 4}}'s Example UUID starting from either Figure 2 (binary) or Figure 3 (integer).

## Base32 {#b32}

Base32 alphabets generally use 5 bits per character to encode the original value and produce a 26 character output which may or may not have padding present for a 128 bit UUID. These Base32 Alphabets include, but are not limited to, the standards based Base32 from {{RFC4648, Section 6}}, Base32hex from {{RFC4648, Section 7}}, Douglas Crockford's Base32 {{DCB32}}, Z-Base-32 {{ZB32}}, and {{GEOHASH}}.

Base32 alphabets tend to have the most variance between the characters used by the various alphabets since the implementors can be a bit more selective in what characters are present. {{best_practice_exclusions}}{: format="title"}, alphabet {{best_practice_sorting}}{: format="title"} are almost always present with little overlap between the various implementations.

Base32 tends to be case insensitive since lowercase character a-z are often treated the same as uppercase A-Z and not required to fill out the underlying alphabet. Finally, {{best_practice_special}}{: format="title"} are not usually used with Base32 encoding unless for {{best_practice_padding}}{: format="title"} or {{best_practice_checksums}}{: format="title"} features which can usually be omitted entirely. There are far too many other Base32 Alphabet variants to cover in this document. As such refer to {{best_practices}}{: format="title"} while selecting an alternate Base32 alphabet not listed in this section.

Base32 is a great choice for implementations where it is available with either Base32hex {{RFC4648}} or Douglas Crockford's Base32 {{DCB32}} being the recommended option for a 26 character UUID that provides a lot of great features for machine or human interactions.

Z-Base-32 {{ZB32}}{: format="title"} makes to many changes to the underlying alphabet order to accommodate a very custom use-case and cannot be recommended for use with UUID. Although {{GEOHASH}} is closer to Base32hex with some specific characters removed as seen in Douglas Crockford's Base32 {{DCB32}} the logic of removing trailing zero's is a something that no other library does. Further it is possible that the {{best_practice_availability}}{: format="title"} may prove challenging for implementors trying to leverage GEOHASH Base32 in their applications. Finally, the regular Base32 from from {{RFC4648, Section 6}} is a fine choice but when compared to base32hex the sorting benefits make it a superior alphabet.

~~~
V0EKVBJTTG8T19R502GCI7JBUO
~~~
{: #sampleBase32HexUUID title='Example UUID encoded as Base32 Hex'}

~~~
Z0EMZBKXXG8X19V502GCJ7KBYR
~~~
{: #sampleBase32Crockford title='Example UUID encoded as Douglas Crockford's Base32'}

TODO add NCNAME
TODO mention Base32-SLUGS?

## Base36 {#b36}

TODO Base36

## Base58 {#b58}

TODO NCNAME, Bitcoin

## Base62 {#b62}

TODO IEEE Base62

## Base64 {#b64}

TODO Base64 Base, URl Safe, NCNAME, Base64UUID

## Base85 {#b85}

TODO ASCII85 (BTOA), Z85

# UUID Encoding Best Practices {#best_practices}

There are a number of factors which require consideration when choosing the best encoding type for a UUID. What is applicable and important to one use case may not be important to another. As such the authors have compiled all of the various considerations into a set of best practice categories in the upcoming sections.

Each section attempts to discuss the topic in relation to its usage with UUID however many of the topics below can be generalized into considerations for selecting an alternate encoding for any data input.

## Usage {#best_practice_usage}

Determining how a UUID will be used is arguably the most important exercise one can do when considering alternate encodings. The result of this analysis will influence all of the following sections in one way or another. UUIDs are used anywhere and everywhere, thus the ubiquitous nature of UUIDs means that there is no single answer to this question and it is one either the implementor or the consumer must both consider.

Take for example a UUID used in text as a logging prefix to uniquely trace and debug functionality through an app. The frequency and size of these logs leans towards a a smaller, compact UUID encoding being preferred over something overly verbose.

If a UUID is to be used by humans in verbal or written situations one may want to omit specific characters as to avoid confusion. Similarly a shorter UUID encoding may also preferable when the characters of the UUID needs to be recited one-by-one over the telephone to remote support to perform some operation. Here both a smaller UUID with character exclusions is preferable to decrease the likelihood of mistakes.

UUIDs used within databases, covered in RFC9562, tend to care about case sensitivity, alphabet ordering and size of the actual encoding which impact both sorting, storage and performance when operating at maximum scale.

UUIDs sent across the wire between machines may benefit from a more compact overall form which reduces bytes on the wire and thus assists with Maximum Transmission Unit (MTU) on limited bandwidth, highly latent networks.

UUIDs used by Internet of Things (IoT) Devices may lean towards an alternate encoding algorithm which is the least computationally expensive as these devices are often limited in that respect.

## Maximum Character Length {#best_practice_length}

The number of characters in a given encoding alphabet is directly correlated to the size of the alternate UUID encoding output.

As a starting point the encodings defined by RFC9562 are Binary, Integer, and Hex. Base02 (Binary) version of a UUID is 128 characters in length consisting of 0s and 1s.The Base10 (Integer) version of a UUID is 39 characters consisting of characters 0 through 9 while the Base16 (Hex) version of a UUID shortens this to 32 characters by using characters 0 through 9 and A through F.

Base32 libraries tend to produce a UUID output of 26 characters in length and base64 drops the output UUID to 22 characters.

With this trend one may be tempted to simply pick the highest BaseXX encoding available and get the smallest encoding output possible however as the output encoding decreases, the base alphabet increases. At a specific point both numeric values, upper and lowercase values may be present and any number of unique symbols can be present in the base alphabet. The addition and ordering of these can have rippling effects that one must properly consider. Further, some of these alphabets require unique math to properly apply to the fixed-length 128 bit UUID which may increase computational complexity in unfavorable ways along with padding to output a similarly fixed-length value.

Simply picking the biggest base alphabet to get the smallest encoding only works if the only consideration an implementation cares about is the actual size of the output UUID and nothing else.

To gleam the maximum size of a given BaseXX encoding convert the binary form of UUID MAX from {{RFC9562, Section 5.10}}.

### Padding {#best_practice_padding}

Padding and padding characters go hand in hand with the previous section involving the maximum character length of an output alternate UUID encoding.

The most common padding character is the equal sign (=) however it could theoretically be any character possible. It is recommended to use the equal sign since it is the most well-known padding character among the various BaseXX encodings.

Further, this character is almost always in the least significant, right-most section of a given alternate UUID encoding. The padding SHOULD stay in this position and moving the character can have ramifications for sorting.

The inclusion or removal of the padding character determines if a given alternate encoding is to be fixed-length or a variable-length. If an implementation requires that a given UUID alternate encoding always be the same length then padding characters MUST be used. If fixed-length outputs are not a problem then the padding character MAY be omitted.

For BaseXX values that do not nicely divide with the 128 bit UUID the padding character may be seen more frequently than others that do have clean byte divisions.

### Checksums {#best_practice_checksums}

Some newer BaseXX encodings include checksum characters that are used to ensure the input/output encoding and decoding is working as expected.

The actual character and algorithm used varies among BaseXX implementations.

Including checksums increases both the maximum size of the output encoding and the computation requirements for encoding/decoding alternate UUID formats.

For alternate UUIDs transmitted among two machines it may be beneficial to include a checksum character to validate the given UUID has not been modified during transport before using it for various operations thus increasing the resiliency of the alternate UUID generation and parsing process.

For applications that simply care about generating UUIDs but do not need to parse, checksums may be omitted entirely.

## Special Characters {#best_practice_special}

After all of the alpha-numeric characters are used a BaseXX alphabet must resort to using special characters such as but not limited to underscore (_), hyphen (-), plus (+), forward slash (/), dollar sign ($), etc. This usually occurs around 62 characters (ten digits 0-9, 26 lowercase english characters, 26 uppercase english characters) but may happen with earlier BaseXX alphabets when specific characters are excluded.

The inclusion of these characters decrease the overall size of the output UUID but have implications in regards to not just sorting and readability but often application usage. For example Base64 as defined by {{RFC4648}} has two alphabets which change the special characters to ensure safe usage with URLs and Filenames. When evaluating a baseXX encoding, care must be taken to ensure the special characters are compatible with the desired use case.

## Character Exclusions {#best_practice_exclusions}

Newer BaseXX alphabet may exclude characters and keep only one value present.

This often occurs when multiple characters are similar in shape which which may lead to issues when humans are required to read the characters aloud or manually transpose the characters by hand. Some characters may be excluded for other reasons. For example it may be advantageous to remove 0 as a possible option to eliminate the possibility of leading 0s in the output data which may lead to problems in specific use cases where leading 0s are not permitted or may be inadvertently removed by the application. Finally, in some smaller BaseXX alphabets it is possible to exclude characters because they are not required to fill out the space. The notable example is Base32hex defined by {{RFC4648, Section 7}} which only uses A through V because the remaining slots are filled by numeric 0-9.

The following character groupings illustrate some of the problematic characters that are similarly shaped.

~~~
- 0, O, o
- 1, I, i, L, l
- 2, Z, z
- V, v, U, u
~~~

Unfortunately there is no standard for which character is omitted or removed. Some BaseXX alphabets may remove the numeric characters while others may remove one or both of the english characters often replacing them with symbols in larger BaseXX alphabets to fill the gaps.

Implementations should vet BaseXX alphabets with character exclusions thoroughly to ensure any possible inclusion of symbols does not cause problems while also verifying that sorting is not impacted if sorting is a requirement.

## Case Sensitivity {#best_practice_sensitivity}

Case Sensitivity becomes important when the alphabet is above 32 characters. For alphabets below Base32, such as Base16, uppercase (A-Z) and lowercase (a-z) characters may be used interchangeably however in larger BaseXX alphabets the uppercase and lowercase alphabetic characters represent distinct output encodings.

In most scenarios uppercase are placed before lowercase counterparts in the ordering. This ordering directly impacts sorting since most applications will sort uppercase characters before lowercase characters.

The BaseXX alphabet used also changes comparison logics where "aBc does not necessarily qual "AbC" in all output encodings.

## Sorting and Ordering {#best_practice_sorting}

Lexicographical sorting of UUIDs is a very important factor to many applications and the ordering of the BaseXX alphabet directly impacts the sorting of the encoded UUID output.

The alphabets generally consist of two or more of the following 4 components with optional omissions as defined by {{best_practice_exclusions}}:

~~~
1. Numeric Characters: 0-9
2. Uppercase Character: A-Z
3. Lowercase Characters: a-z
4. Symbols: underscore (_), hyphen (-), plus (+), forward slash (/), dollar sign ($), etc.
~~~

The ordering of these is traditionally Numeric, Uppercase, Lowercase and Symbols.

It should be noted that some symbols like the dollar sign ($) may sort before numbers while other special such as an underscore (_) will be sorted after the uppercase characters but before the lowercase characters if sorted via their ASCII values.

If an implementation would like to ensure the sort order of the encoded value remains the same as the Base02 (binary) or Base10 (integer) value one should scrutinize the position of the underlying BaseXX alphabets, their ordering and the includes symbols.

Some Examples to illustrate the ordering trends:

~~~
- Base10: 0123456789
- Base36: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
- Base62: 0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
- Base64: ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_
~~~

## Encoding Availability {#best_practice_availability}

The BaseXX alphabet encoding availability may be the second greatest hurdle implementations navigate as they chose a viable alternate encoding for UUID.

For example, traditional Base64 url safe sees far more implementations than Base32hex while even fewer have implemented Base62 or even Crockford's Base32 variant.

The ability to utilize a given encoding or provide a customized alphabet is often a limiting factor in implementations. Implementors will need to write their own, leverage third-party libraries or select a viable alternative. 

## Computation {#best_practice_computation}

The various base encodings have more or less computational requirements based on the size of the alphabet, the total number of bits encoded per character, if checksums are involved, and if floating point math is required.

For reference Base02 uses 2 bits per character encoding while Base10 uses 4 bits per character and Base32 uses 5 bits and Base64 uses 6 bits.
While non non-power-of-two alphabet BaseXX alphabets like Base36, Base58 or Base62 use 5.16, 5.86 5.95 bits respectively where computation is log2(XX) and XX is the alphabet length.

Base62 is desirable by being the largest BaseXX alphabet to use only alpha-numeric symbols but it comes with the tradeoff of slightly more complicated computations. 

## Application Specific {#best_practice_applications}

Some applications have very specific restrictions which may influence the alternate UUID encoding selection. This is a limited list compiling some known restrictions that implementations may consider while working with alternate UUID encodings.

### URI, URL, URN {#best_practice_uri_url_urn}

Uniform Resource Identifiers (URIs) have a specific list of reserved characters defined by {{RFC3986, Section 2.2}} which require percent encoding to be used.

It is advisable to avoid these using BaseXX alphabets that include these symbols.  

### DNS Record {#best_practice_dns}

Domain Name Systems (DNS) are case insensitive with the period or dot character (.) being a reserved symbol. {{RFC9499, Section 2}}

Thus BaseXX alphabets that utilize this character or use upper and lowercase values as defined by {#best_practice_sensitivity} should be avoided.

### XML, HTML, CSS {#best_practice_markup}

Extensible Markup Language ({{XML}}) namespaces cannot start with a leading numeric character.

While older versions of Hypertext Markup Language ({{HTML}}}) had restrictions on element identifiers starting with a leading digit. HTML5 does not have any such restrictions.
The same goes for Cascading Style Sheet ({{CSS}}) selectors identifiers where older versions would have issues when identifiers start with digits.

Thus for XML, HTML and CSS is is advantageous to use an alternate UUID encoding such as {{NCNAME}} to ensure that the first digit is always a alphabet character.

## Other {#best_practice_other}

TODO COPYING, PARSING

# Security Considerations {#security_considerations}

Section {{best_practice_checksums}} is the only section that deals with any security-related items and this only covers data integrity validation for UUIDs sent across the wire. Otherwise there are no other security considerations.

# IANA Considerations {#iana_considerations}

This document has no IANA actions.

--- back

# Acknowledgments {#acknowledgements}

TODO acknowledge.

# Changelog {#changelog}

{:removeInRfc}

draft-00:

{: spacing="compact"}
- Initial Release

# Test Vectors {#test_vectors}

TODO test vector encoding for any UUID present in 9562 text and appendix

# Example UUID Base Encodings {#examples}

The following list of libraries, tools, code, packages and other resources is for illustrative and research purposes.

Neither the authors or IETF endorse any of these libraries or guarantee their contents safe and harmless.

Install, download or use this software at your own risk.

## Base32, base

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

## Base32, Other

~~~
- https://brandur.org/fragments/base32-slugs
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

## Base57

~~~
- https://github.com/lithammer/shortuuid
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

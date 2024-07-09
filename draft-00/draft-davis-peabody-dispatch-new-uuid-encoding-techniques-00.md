---
v: 3
docname: draft-davis-peabody-dispatch-new-uuid-encoding-techniques-00
cat: std
updates: '4122'
consensus: 'true'
submissiontype: IETF
pi:
  strict: 'yes'
  toc: 'yes'
  tocdepth: '3'
  symrefs: 'yes'
  sortrefs: 'yes'
title: New UUID Encoding Techniques
abbrev: new-uuid-format
area: ART
wg: dispatch
kw: uuid
date: 2022

author:
- initials: BGP.
  name: Brad G. Peabody
  org: ''
  email: brad@peabody.io
- ins: K. Davis
  name: Kyzer R. Davis
  org: ''
  email: kydavis@cisco.com
normative:
  RFC4122:
  RFC4648:
  RFC8141:

--- abstract


This document presents new Universally Unique Identifier (UUID) encoding
techniques which facilitate greater efficiency in storage or transport versus
the unnecessarily verbose text representation from {{RFC4122}}.
Furthermore UUID can now be been extended beyond 128 bits to facilitate enhanced
collision resistance or for embedding additional data within a given UUID
value.

--- middle

# Introduction {#Background}

The original "hex and dash", 8-4-4-4-12, format of UUIDs defined by {{RFC4122}} represents a 128 bit UUID value as a 288 bit value.
While this may be great for human readability; the only available UUID text
representation suffers poorly for storage, transmission and application parsing.

Furthermore, UUIDs static length of 128 bits has lead to many alternative,
non-standard, Globally Unique Identifiers which aim to pack more information
into a larger bit space.


# Terminology

## Requirements Language {#requirements_language}

{::boilerplate bcp14-tagged}


## Abbreviations {#acronyms}

The following abbreviations are used in this document:

{: indent="14"}
UUID
: Universally Unique Identifier {{RFC4122}}

URN
: Uniform Resource Name {{RFC8141}}



# changelog {#changelog}
{:removeinrfc}

draft-00:
: * Initial Release


# Extended URN UUID Namespace {#extended_uuid_namespace}

The Existing UUID URN introduced by {{RFC4122}} will remain as
urn:uuid:{uuid_value} however a new, extended URN structures
will be leveraged to convey finer topics introduced by this document.

The UUIDs defined by this document MUST leverage the extended URN UUID namespace
such that the encoding technique and length of the UUID are easily parsable
using {{RFC8141}} compatible logic.
If an application does not care about parsing, converting, or otherwise ascertaining
information from a given UUID value then that application MAY choose to simply
store the actual UUID value in the desired format and omit any UUID URN information.

The Extended UUID namespace syntax is composed of the following parts:

{: vspace='0'}

urn
: Uniform Resource Name as defined by {{RFC8141}}

uuid
: Universally Unique IDentifier as defined by {{RFC4122}}

{uuid_value}
: The actual UUID Value which may have been encoded using
  {{uuid_encoding}} or longer than 128 bits as per {{uuid_long}}.
  Any colon character (:) within this field MUST be percent-encoded as
  "%3A"

{uuid_length}
: Variable entry which describes the total length of encoded UUID Value (uuid_value)
  as an integer value.

{uuid_encoding}
: Variable entry which describes the encoding of the UUID Value (uuid_value).
  This value MAY include dashes characters thus base32 is the same as base-32.

The existing UUID URN is equivalent to an Extended UUID URN via the following
syntax where "base16hd" is a shorthand key for the 128 bit, base16 "Hex and
Dashes" format introduced by {{RFC4122}}.


~~~~
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d:128:base16hd
~~~~
{: title='Extended URN UUID Example:' post='foldleft4'}

The next section defines some other Extended UUID URN namespace usage examples
for alternate encodings for the same UUID Version 4 example as Base 16 Hex
and Dashes (base16hd), Base2 (Binary), and Base10 (Decimal) values.


~~~~
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d:128:base16hd
urn:uuid:1110011111010010100111111100000111010010101000101000001010100111010101011110011010100001110010011100110000010001001110110011101:128:base2
urn:uuid:154072644812979270675542139334331768221:128:base10
~~~~
{: title='Extended URN UUID Example:' post='foldleft4'}


# Alternate Encodings {#uuid_encoding}

The existing UUID hex and dash format of 8-4-4-4-12 is retained for both
backwards compatibility and human readability. This format MUST be implemented
for all UUIDs.

Where required, UUIDs defined by this specification and {{RFC4122}} MAY be encoded utilizing new techniques such as, but not limited to, Base32,
Base36, or Base64.
Applications MAY also utilize other encoding techniques such as modulo division
or alternate alphabets such as Crockford's base32.
When selecting an alternate encoding implementations SHOULD take into consideration
items such as: uppercase/lowercase/mixed character encoding for application
sortability, special character inclusions or omissions, and the exclusion
of key letters like "I", "L", "O", and "U" which may impact sortability of
UUID values and collision resistance provided by all UUIDs.

When utilizing alternate encoding techniques with other applications;
the encoded UUID MUST be prefixed with extended "urn:uuid:" to signal that
the value therein is that of a UUID.
Additionally the UUID MUST and post-fixed with the required extended UUID
URN values as defined by {{extended_uuid_namespace}} to signal the alternate
encoding technique in use and the length of the underlying encoded UUID.

The conversion from UUID to a new encoding technique SHOULD be done utilizing
the underlying integer or binary value for a given UUID versus converting
the hex representation. Two examples for UUIDv4 encoded as base-32 and base-36
are included below.


~~~~
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d:128:base16hd
urn:uuid:3JT57U1QAH859QLSQGSJJ0H7CT:128:base32
urn:uuid:6V1GDX0NNGFV95EFF5TPJ0TR1:128:base36
~~~~
{: title='Alternate Encoding Examples' post='foldleft4'}

For information on converting a UUID into Base 16, Base 32 or Base 64 refer
to {{RFC4648}}.

Furthermore, when utilizing alternate encoding techniques along with Extended
UUID URN namespaces; remember that disallowed characters MUST be percent-encoded
as per {{RFC8141}}.


# UUID Long {#uuid_long}

UUID Long generally references any variable length UUID longer than 128 bits.
There are two main driving factors behind extending UUID beyond 128 bits:

{: vspace='0'}

Increased Entropy:
: While existing UUID formats provide sufficient entropy for most use cases;
  there exists scenarios where even more entropy is required to further reduce
  collision probabilities or guessability.

Requirements for additional embedded items:
: Some implementations require more than 128 bits to properly embed all of
  the application specific data they require.
  Some examples include database metadata like entity types, checksum values,
  shard/partition identifiers, and even node identifiers for distributed UUID
  generation.

UUID Long is compatible with any UUID specified in this document and {{RFC4122}}.
UUID long is variable in length and there is no minimum or maximum value
for UUID Long; however, UUID Long SHOULD leverage natural byte boundaries.
UUID long is defined by appending an additional dash to the existing 8-4-4-4-12
hex and dash format to create 8-4-4-4-12-{variable_length} text format.

The next example shows a UUID Long +32, +64 and +128 example for UUIDv4 with
random data filling the values beyond 128 bits.


~~~~
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d:128:base16hd
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d-05fa17aaac4f8954d9fac2df2fce30f6:160:base16hd
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d-02df3f4babc63a3b2775e27804528a6d23c679eed60fe31df3f8b014e7915595:192:base16hd
urn:uuid:73e94fe0-e951-4153-aaf3-50e4e6089d9d-8fe05412f24ca9974637a10c0b372a7109fb810f61068c0074b480f09f18b30fe03ae7bf09c98b241e8c9c0d51516b963ac75abe447dbff34177093b4b781a46:256:base16hd
~~~~
{: title='UUID Long Examples' post='foldleft4'}

Where required, for compatibility with legacy UUID implementations, the most
significant, left-most, 128 bits MUST be leveraged to truncate from UUID
Long back to a regular UUID.

UUID Long values MAY be coupled with alternate encoding techniques described
in this document.


# IANA Considerations {#IANA}

If Adopted, this document SHOULD be listed in https://www.iana.org/assignments/urn-namespaces/urn-namespaces.xhtml.


# Security Considerations {#Security}

This document has no security considerations.

--- back

# Acknowledgements {#Acknowledgements}

The authors gratefully acknowledge the contributions of
Ben Campbell,
Ben Ramsey,
Fabio Lima,
Gonzalo Salgueiro,
Martin Thomson,
Murray S. Kucherawy,
Rick van Rein,
Rob Wilton,
Sean Leonard,
Theodore Y. Ts'o.,
Robert Kieffer,
sergeyprokhorenko,
LiosK
As well as all of those in the IETF community and on GitHub to who contributed
to the discussions which resulted in this document.


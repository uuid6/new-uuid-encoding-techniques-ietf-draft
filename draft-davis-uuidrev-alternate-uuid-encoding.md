---
v: 3
docname: draft-davis-uuidrev-alternate-uuid-encoding
cat: std
updates: '9562'
consensus: 'true'
submissiontype: IETF
pi:
  strict: 'yes'
  toc: 'yes'
  tocdepth: '3'
  symrefs: 'yes'
  sortrefs: 'yes'
title: Alternate UUID Encoding Techniques
abbrev: alternate-uuid-encoding
area: ART
wg: uuidrev
keyword: uuid
venue:
  group: "Revise Universally Unique Identifier Definitions (uuidrev)"
  type: "Working Group"
  mail: "uuidrev@ietf.org"
  arch: https://mailarchive.ietf.org/arch/browse/uuidrev/
  github: uuid6/new-uuid-encoding-techniques-ietf-draft
  latest: https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/draft-davis-uuidrev-alternate-uuid-encoding.md
date: 2024

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
  RFC8141:

informative:

--- abstract


This document presents new Universally Unique Identifier (UUID) encoding
techniques which facilitate greater efficiency in storage or transport versus
the unnecessarily verbose text representation from {{RFC9562}}.

This document updates {{RFC9562}} and provides alternate encoding techniques which perform better on the wire, in a database, or withing various other application logics.


--- middle

# Introduction {#Background}

The original "hex and dash", 8-4-4-4-12, format of UUIDs defined by {{RFC9562}} represents a 128 bit UUID value as a 288 bit value.
While this may be great for human readability; the only available UUID text representation suffers poorly for storage, transmission and application parsing.

# Terminology

## Requirements Language {#requirements_language}

{::boilerplate bcp14-tagged}


## Abbreviations {#acronyms}

The following abbreviations are used in this document:

{: indent="14"}
UUID
: Universally Unique Identifier {{RFC9562}}

URN
: Uniform Resource Name {{RFC8141}}

# Alternate Encodings {#uuid_encoding}

The existing UUID hex and dash format of 8-4-4-4-12 is retained for both
backwards compatibility and human readability. This format MUST be implemented
for all UUIDs.

Where required, UUIDs defined by this specification and {{RFC9562}} MAY be encoded utilizing new techniques such as, but not limited to, Base32,
Base36, or Base64.
Applications MAY also utilize other encoding techniques such as modulo division
or alternate alphabets such as Crockford's base32.
When selecting an alternate encoding implementations SHOULD take into consideration
items such as: uppercase/lowercase/mixed character encoding for application
sortability, special character inclusions or omissions, and the exclusion
of key letters like "I", "L", "O", and "U" which may impact sortability of
UUID values and collision resistance provided by all UUIDs.

When utilizing alternate encoding techniques with other applications;
the encoded UUID MAY be prefixed with extended "urn:uuid:" to signal that
the value therein is that of a UUID.

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

# changelog {#changelog}
{:removeinrfc}

draft-00:
: * Initial Release
# Updates

#### Draft 00 Work in Progress: 
- [HTML Draft 00](https://uuid6.github.io/new-uuid-encoding-techniques-ietf-draft/draft-00)
- [Plaintext Draft 00](https://raw.githubusercontent.com/uuid6/new-uuid-encoding-techniques-ietf-draft/master/draft-00/draft-davis-peabody-dispatch-new-uuid-encoding-techniques-00.txt)

---

# New UUID Encoding Techniques
This is the GitHub repo for the IETF draft surrounding the topic of new UUID encoding techniques.
Various discussion will need to occur to arrive at a standard and this repo will be used to collect and organize that information.

Note: This was split out of the [New UUID Format draft for UUIDv6, UUIDv7, UUIDv8 and Max UUID](https://github.com/uuid6/uuid6-ietf-draft); some discussions may link to comments on that repo.

## High Level Overview
1. Extend URN as a method to describe the length of new UUID along with the encoding technique of the new UUID.

    `urn:uuid:{uuid_value}:{uuid_length}:{uuid_encoding}`
2. Allow other encoding techniques beyond the default "hex and dashes" format.

3. Allow UUIDs larger than 128 bits. e.g 160 bit UUID

    `8-4-4-4-12-{variable_length}`

---

# RFC Scope
In order to keep things on track the following topics have been decided as in-scope or out of scope for this particular RFC.
For more information on any of these items refer to the XML, TXT, HTML draft, research and the issue tracker for a particular discussion (follow hyperlinks below.)

### In Scope Topics - UUID Generation
- [URN Modifications](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/issues/4)
- [Alternative text encoding techniques (Crockfords Base32, Base64, etc)](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/issues/3)
- [Variable length UUIDs | UUID Long](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/issues/2)


### In Scope Topics - UUID Best Practices as it relates to the previous topics
- Global and Local Uniqueness (collision resistance mechanisms)
- Unguessability
- Any and all UUID security concerns!

---

### Scoping In Progress
- [Variant Bit E Usage](https://github.com/uuid6/uuid6-ietf-draft/issues/26)

---

### Out of Scope Topics (for backwards compatibility)
- Changing the default 8-4-4-4-12 UUID text layout
- Changing anything about RFC4122's UUID versions 1 through 5

---

# Contributing
- The XML draft in the root folder is the most recent working draft for re-submission to the IETF.
  - An HTML and Textual (.txt) RFC representation will be provided in the root folder to ease reader input and discussion.
- The RFC Draft utilize an XML formatted document that follows [RFC7742 markup](https://xml2rfc.tools.ietf.org/rfc7749.html). All XML changes MUST follow this format and pass conversion to `.txt` and `.html` via https://xml2rfc.tools.ietf.org/
- Utilize the issue tracker to discuss topics, solutions, problems, typos and anything else.
  - Where possible contribute to an existing [Discussion Thread](https://github.com/uuid6/new-uuid-encoding-techniques-ietf-draft/issues?q=is%3Aissue+is%3Aopen+label%3ADiscussion) vs creating a new thread.
  - New threads that propose alternative text SHOULD utilize `Proposed Draft Change` GitHub issue template to ensure proper information is captured for the draft authors.
  - Be civil!
- Pull requests will be accepted  *as long as the text is concise, clear and objective.* 
  - PRs will not be accepted for changes to the decision made for the draft without full discussion. 
  - PRs MUST include the updated `.xml` and xml2rfc generated `.txt` and `.html` documents.
  - Draft versions are frozen until submission to the IETF; at which point new work constitutes a new draft version.
- For any examples UUIDv4 `73e94fe0-e951-4153-aaf3-50e4e6089d9d` MUST be used as the common UUID for describing modifications.

---

# Prototyping
Remember first and foremost that this specification is still a draft. Breaking changes are to be expected. Prototypes SHOULD only be implemented to verify or discredit topics of the draft text.
- [Prototype Implementations](https://github.com/uuid6/prototypes) are available via this repro.

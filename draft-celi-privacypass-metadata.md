---
title: "Privacy Pass and Public Metadata"
docname: draft-celi-privacypass-metadata-latest
category: info

ipr: trust200902
area: General
keyword: Internet-Draft

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: S. Celi
    name: Sofía Celi
    organization: Cloudflare
    email: cherenkov@riseup.net

normative:
  RFC2119:

informative:



--- abstract

Privacy Pass is a protocol in which clients are allowed to receive
one-time-use anonymous tokens, using an issuance protocol, that can later
be used to anonymously authenticate themselves using a redemption protocol.

Certain instantiations of it may want public metadata to be cryptographically
bound to the issued token for different applications, such as prevention
against hoarding attacks. This document specifies how to add this public
metadata by using the PO-PRF construction.

--- middle

# Introduction

TODO Introduction


# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

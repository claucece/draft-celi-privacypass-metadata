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

Certain instantiations of Privacy Pass may want public metadata to be
cryptographically bound to a issued token. Several mechanisms have been
proposed for it as, for example, attribute-based VOPRFs (ABVOPRF) and Anonymous
Credentials in the realm of contact tracing.

In this work, we use PO-PRFs for adding metadata to tokens in a Privacy
Pass context, as it is efficient, easy to integrate into the API, allows
for an arbitrarily long metadata, and achieve the same security properties as
needed by the protocol.

We will abide in this document by the definition of public metadata as
defined on the architectural document of Privacy Pass: "Public metadata is
that which clients can observe as part of the token issuance flow".
We will also determine public metadata as transparent (if both the
client and server know the metadata and can check it for correctness) and
opaque (if both the client and server know the metadata but the client cannot
check it for correctness).

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

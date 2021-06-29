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

# Protocol description {#overview}

The Privacy Pass protocol with metadata is split into two phases.

The first phase, "issuance", provides the client with unlinkable tokens
that can be used to initiate re-authorization with the server in the
future. The second phase, "redemption", allows the client to redeem a
given re-authorization token with the server that it interacted with
during the issuance phase. The protocol must satisfy two cryptographic
security requirements known as "unlinkability" and "unforgeability".

## Server setup {#server-setup}

Before the protocol takes place, the server chooses a ciphersuite and
generates a keypair by running `(pkS, skS) = KeyGen()`. This
configuration must be available to all clients that interact with the
server (for the purpose of engaging in a Privacy Pass exchange). We
assume that the server has a public (and unique) identity that the
client uses to retrieve this configuration.

The server can also choose the metadata that is going to be bounded
into the tokens, or wait for the client to determine the metadata.

## Client setup {#client-setup}

The client initialises a global storage system `store` that allows it
store the tokens that are received during issuance. The storage system
is a map of server identifiers (`server.id`) to arrays of stored tokens.
We assume that the client knows the server public key `pkS` ahead of
time. The client picks a value `m` of tokens to receive during the
issuance phase.

The client can also choose the metadata that is going to be bounded
into the tokens, or wait for the server to determine the metadata.

## Issuance phase {#issuance-phase}

The issuance phase is a two-round protocol that allows the client to
receive `m` anonymous authorization tokens bounded to a metadata `tag`
from the server. The first round sees the server generate a commitment.
The second round sees the server issue a token to the client.

~~~
  Client(pkS, m, tag, info)                        Server(skS, pkS)
  ------------------------------------------------------------

  commit_req = Prepare(info)

                            commit_req
                      ------------------->

                        commit_resp = Commit(skS, pkS, commit_req)

                            commit_resp
                      <-------------------

  cInput = Generate(m, tag, commit_resp)
  req = cInput.req

                              req
                      ------------------->

                              issueResp, tag  = Issue(pkS, skS, tag, req)

                         issueResp, tag
                      <-------------------

  tokens = Process(pkS, cInput, issueResp)
  store[server.id].push(tokens, tag)
~~~

## Redemption phase {#redemption-phase}

The redemption phase allows the client to anonymously reauthenticate to
the server, using data and metadata that it has received from a previous
issuance phase.

~~~
  Client(info)                                Server(skS, pkS)
  ------------------------------------------------------------
  token, tag = store[server.id].pop()
  req = Redeem(token, info, tag)

                               req
                        ------------------>

                               if (dsIdx.includes(req.data)) {
                                 raise ERR_DOUBLE_SPEND
                               }
                               resp = Verify(pkS, skS, req, tag)
                               if (resp.success) {
                                 dsIdx.push(req.data)
                               }

                                resp
                        <------------------
  Output resp
~~~

# Token Expiration as Token Metadata

One abuse of Privacy Pass not prevented by the original design is
what we refer to as a hoarding attack, also called a farming attack.
A malicious user can gather a large number of tokens by running the
issuance protocol as many times as possible over some period of time and
getting `m` amount of tokens. Later, the malicious user can redeem all the
gathered tokens at once in order to render the provided service unavailable
in a (D)DoS attack (by, for example, overwhelming a website with expensive
requests).

One potential defense against this attack is to force periodic rotation
of the server's key as a mean to revoke tokens. Nevertheless, token issuance
associates all issued tokens with a particular choice of key and, if a server
issues tokens with many keys, then this may harm the anonymity of
the Client, as the anonimity set gets diminished.

A solution is to use the PO-PRF construction to add metadata to the issuance
and redemption phases: this metadata should be associated with the
timestamp in which a token was issued but it MUST not explicitly use it:
using an explicit timestamp could harm the anonimity of the
tokens as a server will be able to link the issuance and redemption
phases by looking at when the token was generated and later redeemed.

The proposed metadata to be used in this case is key rotation epochs (`n`).
A server will still need to rotate their keys to prevent against
key-compromise and credential stuffing. Each key rotation will be assigned
a number `n` starting from the first generated key `0`. Every time that
a key rotates, this number will be increased by 1. This epoch number `n` will
be cryptographically bounded (added as metadata) to the tokens by the server
during the `Issue` phase, and stored by the client alongside with the tokens.
s
During the redemption phase, the client will send this stored epoch (the number `n`)
alongside the token. The server will check if their epoch is smaller than
the client's sent one. If it is smaller, the token is expired. If it is
the same, the token is valid. If it is bigger, the client might be trying
to trick the server: this attack won't work as the Verification procedure
executed by the server will fail.

The anonimity set is still reduced depending on the key schedule of the server.
This metadata is opaque to the client but verifiable by the server.

// TODO: we can also stop rotation, and invoke a server policy of
rotating epochs.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

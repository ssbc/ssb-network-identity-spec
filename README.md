# Network Identity Spec

In classical SSB the identity you use over the network during secret a
handshake connection is the same as your feed identity. With [meta
feeds] it is possible to have multiple feeds under one meta feed, this
could be a main feed and a chess application feed. It should be
possible to write applications in such a way that a main social
application does not need to store the actual chess application feed
and vice versa. One should be the master writer to the meta feed, but
can share the relevant keys needed with the other application. Thus we
would like to introduce the concept of network identity where multiple
keys in the same meta feed can be used over the network while still
tying them together.

Furthermore these keys could overlap with the feed keys but don't have
to. From a security point of view this would allow a network process
to only know a key used for shs connections. So a compromise would not
be the end of the world. Another use case this allows is encrypted
network identities. Meaning only certain people would know that a
network identity is you.

In the following we define the message structure for the messages
written to a network identity feed under the root meta feed.

```
{ 
  "type" => "metafeed/add/derived", 
  "feedpurpose" => "network-identities",
  "subfeed" => (BFE-encoded Bendy Butt feed ID),
  "metafeed" => (BFE-encoded Bendy Butt feed ID for the meta feed),
  "nonce" => (bencode byte sequence with 32 random bytes),
}
```

## Message structure

The message structure on the network identities feed will use the
[bendy butt] feed format as they look very similar to meta feed
messages themselves. The messages need to be double signed in order to
prove that the author is in possesion of both keys.

## Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in RFC 2119.

We use bencode and BFE notations as defined in the [bendy butt] spec.

## Usage of Bendy Butt feed format

Network identity **MUST** use the [bendy butt] feed format with a few
additional constraints.

The `content` dictionary inside the `contentSection` of network
identity messages **MUST** conform to the following rules:

 - Has a `type` field mapping to a BFE string (i.e. `<06 00> + data`)
 which can assume only one the following possible values:
   - `network-identity/add`
   - `network-identity/tombstone`
 - Has a `network-identity` field mapping to a BFE "network identity
   ID", i.e. `<07 01> + data`
 - Has a `metafeed` field mapping to a BFE "Bendy Butt feed ID", i.e.
 `<00 03> + data`

The `contentSignature` field inside a decrypted `contentSection`
**MUST** use the `identity`'s cryptographic keypair.

Note the data part of the BFE encoded network identity ID can overlap
with the data part of a BFE encoded feed ID, but does not have to.

Example content part of a message:

```
{
  "type" => "network-identity/add",
  "network-identity" => (BFE-encoded network identity ID),
  "metafeed" => (BFE-encoded Bendy Butt feed ID for the meta feed),
  "tangles" => {
    "network-identity" => {
      "root" => null,
      "previous" => null
    }
  },
},
```


[secret handshake]: https://github.com/auditdrivencrypto/secret-handshake
[meta feeds]: https://github.com/ssb-ngi-pointer/ssb-meta-feed-spec
[bendy butt]: https://github.com/ssb-ngi-pointer/bendy-butt-spec/

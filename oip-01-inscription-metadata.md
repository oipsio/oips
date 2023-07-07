# OIP-01: Inscription Metadata

**Status:** RFC (Request for Comment)

### Overview

Inscriptions are done using "envelopes" as described on the [Ordinal Theory Handbook](https://docs.ordinals.com/inscriptions.html). While it supports multiple media types, it leaves no rooms for metadata.

Some workarounds potentials:

* Separate coupled inscription for metadata.
* Linkage of metadata on external storage, e.g. IPFS.

However, the above come with its own sets of challenges:&#x20;

1. Inscriptions cannot be linked together purely on-chain without metadata.
2. Linkage via external storage involves an external media, which may not be available universally alongside the Bitcoin blockchain.
3. Linkage via external storage is one-way only, external to ordinal. This may result in multiple metadata pointing to the main one, not knowing which is the canonical one.&#x20;

Therefore, the ideal solution is to have metadata not only fully on-chain, also ideally attached with the same ordinal.

### Solution

A safe way to do that would be to have two inscriptions on the same ordinal. First one being the actual payload, second one being the metadata as follows:

```
OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 1
  OP_PUSH "text/plain;charset=utf-8"
  OP_PUSH 0
  OP_PUSH "Hello, world!"
OP_ENDIF

OP_FALSE
OP_IF
  OP_PUSH "ord"
  OP_PUSH 1
  OP_PUSH "application/json;charset=utf-8"
  OP_PUSH 0
  OP_PUSH "{\"p\":\"oip-01\"}"
OP_ENDIF
```

This is compatible with the current version of Ordinals and is[ fully covered with a unit test](https://github.com/ordinals/ord/blob/master/src/inscription.rs#L474), ignoring further inscriptions after the first one, but treating the first one as a valid inscription.&#x20;

Note that the content in the metadata example above is merely meant as an arbitrary example and not a mandated format, i.e. that it has to has the header `{"p":"oip-01"}`. This document covers how metadata can be carried as part of OIP-01. Format of metadata is out of scope for the intents and purposes of OIP-01.&#x20;

### FAQ

#### 1. What tools are available to parse on-chain ordinal metadata (OIP-01)?

Tools are still being developed and will be updated as tools and libraries are known or available. If you have developed a tool, please submit a pull request to update this document with a link to your tools.

#### 2. Would this break Ordinals for other users/tools/sites that are not aware of OIP-01?

Absolutely not. OIP-01 is backward-compatible. Services that are not aware of OIP-01 will simply be showing only the first inscription, [just as intended](https://github.com/ordinals/ord/blob/master/src/inscription.rs#L474). Metadata will be ignored.&#x20;

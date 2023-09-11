# OIP-06: Royalty

## OIP-06: Ordinals Royalty Standard

Proposer: [U-Zyn Chua](https://twitter.com/uzyn)

**Status:** WIP (Draft: Work-in-progress)

#### Overview

Ordinals collections have been successful. Many creators coming from the Ethereum NFT space are wishing for Royalty support similar to that of [EIP 2981](https://eips.ethereum.org/EIPS/eip-2981) available on the Ordinals.

Royalty can be useful in rewarding creators and to encourage further creation of utilities. Rather than just making only a one-time minting fee during first-mint as implemented at many exchanges, some creators would wish to continue to gain from Ordinals sale that they authored. This could potentially help in encouraging creation of further utilities on Ordinals.

#### Solution

Similar to [EIP 2981](https://eips.ethereum.org/EIPS/eip-2981), royalty on Ordinals would be an honor-based system implemented by marketplaces. The inability to enforce it entirely on-chain are due to the following reasons:

1. The fact that Ordinals Protocol is a social consensus on Bitcoin blockchain, unenforceable by Blockchain consensus.
2. Even if it is technically possible, it is not possible to differentiate between ordinals trading (should warrant payment of royalty) and ordinals transfer (should not warrant payment of royalty).

**Definition**

Royalty is to be defined in percentage of the sale price and in non-negative number format.

* `0` for no royalty
* `0.05` for 5% royalty
* `1` for 100% royalty
* `1.5` for 150% royalty

Definition could be defined via either OIP-02: Verifiable Ordinal Collections or Ordinal Theory's metadata.

Definition via OIP-02: Verifiable Ordinal Collections:

```json
{
  "p": "vord",
  "v": 1,
  "ty": "col",
  "title": "Wizards",
  "desc": "Description of the collection (optional)",
  "url": "URL to collection (optional)",
  "slug": "wizards_(optional)",
  "creator": {
    "name": "Artist #1",
    "email": "artist@example.org",
    "address": "1BitcoinAddress"
  },
  "royalty": {
    "address": "1RoyaltyReceivingAddress",
    "pct": 0.03
  }
}
```

Definition via Ordinals Protocol metadata to be added soon.

**Payment**

UTXO-model of Bitcoin makes it possible to make royalty payment in a single trade transaction.

An Ordinal trade with royalty should consist minimally of the following:

Inputs:

1. Cardinal, from the buyer, minimally sale price + royalty + transaction fee.
2. Ordinal, from the seller.

Outputs:

1. Ordinal, to the buyer.
2. Cardinal, sale price, to the seller.
3. Cardinal, royalty, to the creator.

Royalty should be computed as sale price \* royalty percentage.

Marketplaces supporting Ordinals Royalty Standard should prepare partially-signed Bitcoin transaction (PSBT) as above.

#### FAQ

**1. What about previously defined OIP-02 collections? Can their creator adds royalty?**

It is out of scope of this document. Updating of collection definition should be a separate specification.

**2. Any marketplaces supporting it yet?**

Not that I know of at this moment. [Ordzaar](https://ordzaar.com) and [Sado SDK](https://sado.space) are planning support.

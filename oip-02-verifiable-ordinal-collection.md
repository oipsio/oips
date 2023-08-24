# OIP-02: Verifiable Ordinal Collection

**Status:** RFC (Request for Comment)

This is also commonly known as **Verifiable Ordinals**.

### Overview

Non-fungible token (NFT), esp. on Ethereum, takes off massively not only due to the great artists' contribution ot the space, but largely also due to the following factors:

1. Low costs to NFT artists to get started in launching their collections and arts on Ethereum.
2. Buyer pays for gas (transaction fees) while allowing creators and artists to profit from it.
3. Parentage links, allowing NFTs to be traces to its parent smart contract, therefore knowing that its legitimacy as part of a collection.

It would be remarkable if the same can be achieved for Bitcoin-based ordinal, while staying true to the original intents and ethos of Ordinal Theory. This proposal intends to do that while not violating the [guidelines & boundaries](broken-reference) set out for Ordinal Theory Improvements.

### Roles

The following roles are defined to align on the understanding:

1. Creator
   * Usually the artist.
   * Initiates collection of ordinal inscriptions.
   * Usually, intends to be paid, especially from the first sale of an ordinal in the said collection.
2. Publisher
   * Service, or website, that provides minting service of ordinals that belong to the said collection.
   * Usually also provides collection creation service to the Creator.
3. Purchaser
   * First-hand buyer, and therefore owner, of an ordinal from a collection.
   * Pays the purchase price, of which the bulk of it should go to the Creator.

### Objectives

1. Provide a method where creator, especially artist, who intends to make their artwork available as ordinal inscriptions do not have to start with big capital outlay to cover the inscription costs.
2. Allows first-hand buyers to take ownership of inscribing, and therefore pay for inscription fee.
3. Allows ordinals to be independently verified, if they belong to a specific collection, or otherwise. Verification must be able to be carried out in a fully trustless manner, by anyone, including unrelated third-parties, and in an asynchronous way.
4. Inscriptions trading must be carried out on-chain, just like other ordinals. Off-chain trading of ordinals must not be possible.
5. Adheres to the ideals of ordinal inscriptions, specifically that inscriptions should be fully on-chain and ideally, maintain backward-compatibility to services that implement only the original Ordinal Theory without support of any further improvements.

### Solution

The proposed solution covers the following arbitrary and hopefully general flow of creating, publishing, purchasing, and trading of an ordinal that belongs as part of a collection.

#### Flow

1. Creator initiates a collection, usually a series of ordinal inscriptions with a theme.
2. Creator defines and publishes the collection via the service of a Publisher.
3. Purchaser browses the collection, at this stage uninscribed, at Publisher's site, and decides to purchase a specific item from the collection.
4. Purchaser pays for the purchase via Publisher's site and receives the inscription's payload, e.g. an image, and its metadata, delivered in the form of an unsigned transaction.
   1. Creator receives the bulk of the purchase fees.
5. Purchaser can choose for the right moment to inscribe based on factors such as Bitcoin network fees.
   1. Purchaser, however, would not be able to trade/resell the purchased ordinal without first inscribing them as doing so would invalidate the verifiable ordinal.

### Collection

Collection is to be inscribed as a `text/plain` inscription with the following:

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
  "publ": ["1PublisherBitcoinAddress1", "1PublisherPublicKey1"],
  "insc": [
    {
      "iid": "wiz-01",
      "lim": 10,
      "sri": "sha256-Ujac9y464/qlFmtfLDxORaUtIDH8wrHgv8L9bpPeb28="
    },
    {
      "iid": "IDs as Titles #2",
      "lim": 1,
      "sri": "sha256-zjQXDuk++5sICrObmfWqAM5EibidXd2emZoUcU2l5Pg="
    },
    {
      "iid": "common",
      "lim": 1000
    }
  ]
}
```

Notes:

1. `p` and `v` are required. They refer to protocol and version.
2. `ty` refers to `type`. Only `col` and `insc` are 2 valid values. `col` is used here for collection.
3. `slug` is a suggestion and not a guarantee that the slug would be reserved for the specific collection. Even if `slug` has already taken by other collections before this one, this collection remains valid.
4. `publ` refers to an array of public Bitcoin addresses from publisher. This is intended to be used for verification of inscribed Ordinals.
   * As Bitcoin Core does not support message signing with bech32 address, there are no known standard ways to sign messages with bech32 address today, it is recommended that publisher's address to be of the [legacy](https://bitcoin.design/guide/glossary/address/#legacy-address---p2pkh) type, usually starting with `1`.
   * Alternatively, an array of Bitcoin public keys or a mix of addresses and public keys can also be used, which allows for a wider range of message signing and verification processes.
5. `insc` refers to an array of inscription collections.
   * `iid` refers to inscription ID. It must be unique within a collection. Collision of `iid` invalidates the collection definition.
   * `lim` defines the maximum number of times that an inscription can be sold or inscribed. Further inscriptions beyond `lim` count will be deemed invalid.&#x20;
   * `sri` is an optional field and refers to subresource integrity. It adds resource integrity to inscriptions.
     1. SRI is defined in accordance to [W3C's SRI specifications](https://www.w3.org/TR/SRI/)
     2. If `sri` is present, validation must check for resource integrity. Otherwise, resource integrity check is not needed.
6. Post-inscription of the collection, take note of its genesis txid or current outpoint, which are needed for inscription verification. Either of these may be referred to as `collectionID`.

### Verifiable Ordinal Inscription

Verifiable ordinal inscription is to be inscribed as a valid inscription with an attached metadata, following [Inscription Metadata](oip-01-inscription-metadata.md) recommendations.

Metadata for inscription:

```json
{
  "p": "vord",
  "v": 1,
  "ty": "insc",
  "col": "eb9d4726a7caaaf5a7fe082059cb37b97b188845b70f476fa9833ec3079f5600",
  "iid": "wiz-01",
  "publ": "1PublisherBitcoinAddress1",
  "nonce": 0,
  "sig": "HzXmUa2mXuu7kCog7B7NqSR61Oa/Tr13IixoJG6C3e3qCdKptNL0pT0wxQ8oZGz9hvgjnq4WL+pMSjsqV+lVv94="
}
```

Notes:

1. `p` and `v` are required. They refer to protocol and version.
2. `ty` refers to `type`. Only `col` and `insc` are 2 valid values. `insc` is used here for inscription.
3. `col` refers to either a genesis txid or current outpoint where the collection inscription can be located.
   * If the collection inscription has been moved, `col` , referring to genesis txid and not current location of the collection means that it would remain unchanged.
4. `col` and `iid` as a tuple refers specifically to an inscription.
   * There could be multiple of the same `col` and `iid` up to the `lim` that's defined at collection.
   * Further inscriptions beyond `lim` must not validate.
5. `publ` refers to either one of the publisher's Bitcoin addresses or public keys as defined when creating the collection.
   * `publ` is the key that is being used to sign the message and produce `sig`
6. `nonce` is an incremental integer, starting from 0 to `lim-1`.
   * This is to ensure that there will only be `lim` number of inscriptions per `iid` as specified in the collection definition.
   * `nonce` does not have to be published in order, if `nonce=4` is published before `nonce=3` it is still a valid inscription as long as:
     * `nonce < lim`, and
     * `nonce` is the only one on chain. If nonce collides for the same `iid`, only the first one to be mined in a block, or the first one in the list of transactions in a block will be valid.
7. `sig` refers to the signature generated by the publisher using the address referred in `publ` with the payload being `COLLECTION_ID INSCRIPTION_ID NONCE` where each value is separated by a single whitespace.
   * `sig` generated by publisher using a standard `signmessage` command of Bitcoin Core or an equivalent method in any Bitcoin library.
   * `sig` can be independently verified by anyone using `verifymessage` command of Bitcoin Core or any Bitcoin library.

#### Inscription and UX

1. Publisher MUST provide the purchaser with the following:
   * Raw unsigned inscription payload with the content, wrapped in the first inscription envelope.&#x20;
   * Metadata for verification, wrapped in the second inscription envelope, as defined in [Inscription Metadata](oip-01-inscription-metadata.md).
2. Publisher SHOULD provide the purchaser with the following:
   * Rendered content of the purchased ordinal. This is typically an image, but may also be any content type supported by Ordinal Theory, including but not limited to text, movie, sound, etc.
   * Instructions to inscribe using the purchased address, and not any other addresses.&#x20;
3. Purchaser CAN inscribe the purchased inscription at any time.
4. Publisher SHOULD take note of sold inscriptions and not oversell.
5. Publisher SHOULD forward the commission collected from the fee paid by Purchaser, based on any contracts that the Publisher have with the Creator.
6. Sites and services or marketplaces that support verifiable ordinal SHOULD provide an indicator if an ordinal is a Verifiable Ordinal and SHOULD provide a checkmark if it passes or fails validation.&#x20;
   * If it fails, reason(s) of the failure SHOULD be provided.

#### Signature and Verification

Referring to the example above, signature generation, by the publisher, can be generated as follows, using the standard `signmessage` command of Bitcoin Core:

```bash
$ bitcoin-cli signmessage 13fh1atRp7rV2qdGuxhFabGkmKAnRia9Kq "eb9d4726a7caaaf5a7fe082059cb37b97b188845b70f476fa9833ec3079f5600 wiz-01 0"
H+i8FyNiLVvCqcjOqluKzU+lvy2RY8AGBqlFEgFGgaLALQ3zamtnxo6XzuPuyFD0sKd+5Tz2Q552sbNPb2A0kjo=
```

Verification can be done by anyone using `verifymessage` as follows:

```bash
$ bitcoin-cli verifymessage 13fh1atRp7rV2qdGuxhFabGkmKAnRia9Kq "H+i8FyNiLVvCqcjOqluKzU+lvy2RY8AGBqlFEgFGgaLALQ3zamtnxo6XzuPuyFD0sKd+5Tz2Q552sbNPb2A0kjo=" "eb9d4726a7caaaf5a7fe082059cb37b97b188845b70f476fa9833ec3079f5600 wiz-01 0"
true
```

Alternatively, for an example of signing and verifying using bitcoin-core standards with taproot addresses, the inclusion of a public key is required, but can only be signed and verified using external libraries, with supporting libraries including:

    * [Ordit SDK](https://github.com/sadoprotocol/ordit-sdk)

If `sri` is defined at collection, content integrity verification should also be carried out in accordance to [W3C's SRI specification](https://www.w3.org/TR/SRI/).

### Improvements

Further improvements to OIP-2: Verifiable Ordinal Collection can include:

1. Multi-publisher workflow by allowing Publisher to publish sales on the blockchain.
   * This will allow for better tracking of `lim` beyond single Publisher in order not to oversell.
2. Traits-based inscriptions, where collection is defined as permutations of traits rather than defined as individual inscriptions.

The above may be further published as separate proposals and is intended to not be included as part of this proposal for better focus and clarity.

### Alternatives

Exploring various alternative proposals that are in the similar space:

1. [Generative BRC-721](https://github.com/jerryfane/generative-brc-721)
   * This allows for collection defined via traits-based permutations.
   * While the traits are stored on-chain in a form of stringified data, the resulting artefact is a metadata with instructions on how to recreate the image.
   * This, however, would render on ordinals.com as text but not image and would require sites to specifically support the standard to generate the desired image.
2. [BRC 721 experimental proposal](https://brc-721.gitbook.io/about-the-brc-721-experimental-proposal/)
   * This proposal introduces externally linked (IPFS) images.
   * It does not adhere to [Ordinal Theory's definition of digital artifacts](https://docs.ordinals.com/inscriptions.html), which states that inscription content has to be entirely on-chain.
3. [BRC-721 Ordinals Collection Protocol](https://www.brc721.com)
   * This is a protocol that introduces a similar standard with that of ERC-721 of Ethereum.
   * Content is not on-chain and is externally linked in the form of `tokenURI`.

### FAQ

#### 1. What tools are available to verify Verifiable Ordinals?

Verification of verifiable ordinals can be carried out using standard tooling such as Bitcoin Core, using commands such as `verifymessage`.

Programming-language specific toolings and libraries are still being developed and will be updated as tools and libraries are known or available. If you have developed a tool, please submit a pull request to update this document with a link to your tools.

#### 2. Would this break Ordinals for other users/tools/sites that are not aware of Verifiable Ordinals?

Absolutely not. Verifiable Ordinals is backward-compatible. Services that are not aware of Verifiable Ordinals will simply be displaying ordinals and their inscriptions fine.

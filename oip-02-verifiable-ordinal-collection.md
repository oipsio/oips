# OIP-02: Verifiable Ordinal Collection

**Status:** RFC (Request for Comment)

This is also commonly known as **Verifiable Ordinals**. &#x20;

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
   * Initiates of collection of ordinal inscriptions.
   * Usually, intends to be paid, especially from the first sale of an ordinal in the said collection.
2. Publisher
   * Service, or website, that provides minting service of ordinals that belong to the said collection.
   * Usually also provides collection creation service to the Creator.
3. Purchaser
   * First-hand buyer, and therefore owner, of an ordinal from a collection.
   * Pays the purchase price, of which the bulk of it should go to the Creator.&#x20;

### Objectives

1. Provide a method where creator, especially artist, who intends to make their artwork available as ordinal inscriptions do not have to start with big capital outlay to cover the inscription costs.&#x20;
2. Allows for first-hand buyer to take ownership of inscribing, and therefore pay for inscription fee.
3. Allows ordinals to be independently verified, if they belong to a specific collection, or otherwise. Verification must be able to be carried out in a fully trustless manner, by anyone, including unrelated third-parties, and in an asynchronous way.
4. Inscriptions trading must be carried out on-chain, just like other ordinals. Off-chain trading of ordinals must not be possible.
5. Adheres to the ideals of ordinal inscriptions, specifically that inscriptions should be fully on-chain and ideally, maintain backward-compatibility to services that implement only the original Ordinal Theory without support of any further improvements.

### Solution

The proposed solution covers the following arbitrary and hopefully general flow of creating, publishing, purchasing, and trading of an ordinal that belongs as part of a collection.

#### Flow

1. Creator initiates a collection, usually a series of ordinal inscriptions with a theme.
2. Creator defines and publishes the collection via the service of a Publisher.
3. Purchaser browses the collection, at this stage uninscribed, at Publisher's site, and decides to purchase a specific item from the collection.
4. Purchaser pays for the purchase via Publisher's site and receives the inscription's payload, e.g. an image, and its metadata, delivered in the form of unsigned transaction.
   1. Creator receives the bulk of the purchase fees.&#x20;
5. Purchaser can choose the for the right moment to inscribe based on factors such as Bitcoin network fees.
   1. Purchase, however, would not be able to trade/resell the purchased ordinal without first inscribing them as doing so would invalidate the verifiable ordinal.

### Collection

Collection is to be inscribed as a `text/plain` inscription with the following:

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "p": "vord",
  "v": 1,
  "ty": "col",
  "title": "Wizards",
  "desc": "Description of the collection (optional)",
  "url": "URL to collection (optional)",
  "slug": "wizards",
  "creator": {
    "name": "Artist #1",
    "email": "artist@example.org",
    "address": "1BitcoinAddress"
  },
  "publ": ["1PublisherBitcoinAddress1", "1PublisherBitcoinAddress2"],
  "insc": [
    {
      "iid": "wiz-01",
      "lim": 10,
      "sri": "sha256-Ujac9y464/qlFmtfLDxORaUtIDH8wrHgv8L9bpPeb28="
    },
    {
      "iid": "wiz-02",
      "lim": 1,
      "sri": "sha256-zjQXDuk++5sICrObmfWqAM5EibidXd2emZoUcU2l5Pg="
    }
  ]
}
</code></pre>

Notes:

1. `p` and `v` are required. They refer to protocol and version.
2. `ty` refers to `type`. Only `col` and `insc` are 2 valid values. `col` is used here for collection.
3. `slug` is a suggestion and not a guarantee that the slug would be reserved to the specific collection. Even if `slug` has already taken by other collections before this one, this collection remains valid.
4. `publ` refers to an array of public Bitcoin addresses from publisher. This is intended to be used verification of inscribed Ordinals.&#x20;
   * As Bitcoin Core does not support sign messages with bech32 address,  there are no known standard ways to sign messages with bech32 address today, it is recommended that publisher's address to be of the legacy type, usually starting with `1`.
5. `insc` refers to an array of inscription collections.
   * `iid` refers to inscription ID. It must be unique within a collection. Collision of `iid` invalidates the collection definition.
   * `lim` defines the maximum amount that an inscription can be sold or inscribed. Further inscriptions beyond `lim` count will not validate.&#x20;
   * `sri` is an optional field and refers to subresource integrity. It adds resource integrity to inscriptions.
     1. SRI is defined in accordance to [W3C's SRI specifications](https://www.w3.org/TR/SRI/).&#x20;
     2. If `sri` is present, validation must check for resource integrity. Otherwise, resource integrity check is not needed.
6. Post-inscription of the collection, take note of its genesis txid. That is needed for inscription verification. It can also be referred to as collection ID.

### Verifiable Ordinal Inscription

Veriable ordinal inscription is to be inscribed as a valid inscription with an attached metadata, following [Inscription Metadata](oip-01-inscription-metadata.md) recommendations.

Metadata for inscription:

```json
{
  "p": "vord",
  "v": 1,
  "ty": "insc",
  "col": "eb9d4726a7caaaf5a7fe082059cb37b97b188845b70f476fa9833ec3079f5600",
  "iid": "wiz-01",
  "publ": "1PublisherBitcoinAddress1",
  "owner": "bc1qq0f8gndxqjftxalke9q9kxcgw6da7cp0vwflpj",
  "sig": "HzXmUa2mXuu7kCog7B7NqSR61Oa/Tr13IixoJG6C3e3qCdKptNL0pT0wxQ8oZGz9hvgjnq4WL+pMSjsqV+lVv94="
}
```

Notes:

1. `p` and `v` are required. They refer to protocol and version.
2. `ty` refers to `type`. Only `col` and `insc` are 2 valid values. `insc` is used here for inscription.
3. `col` refers to genesis txid where the collection inscription can be located.
   * If the collection inscription has been moved, `col` , referring to genesis txid and not current location of the collection means that it would remain unchanged.
4. `col` and `iid` as a tuple refers specifically to an inscription.&#x20;
   * There could be multiple of the same `col` and `iid` up to the  `lim` that's defined at collection.&#x20;
   * Further inscriptions beyond `lim` must not validate.
5. `publ` refers to one of the publisher's Bitcoin addresses as defined at collection.
   * `publ` is the key that is being used to sign the message and produce `sig`
6. `owner` refers to the purchaser of the inscription.
   * This is only tracking the first owner, i.e. direct purchaser of the inscription from the publisher, therefore making sure that it's fully authorized by the creator.&#x20;
   * Owner's address can be any types of Bitcoin address, including bech32.
7. `sig` refers to the signature generated by the publisher using the address referred to in `publ` with the payload being `COLLECTION_ID INSCRIPTION_ID OWNER`
   * `sig`  generated by publisher using a standard `signmessage` command of Bitcoin Core.
   * `sig` can be independently verified by anyone using `verifymessage` command of Bitcoin Core.

#### Inscription and UX

1. Publish MUST provide the purchaser with the following:
   * Raw unsigned inscription payload with the content, wrapped in the first inscription envelope.&#x20;
   * Metadata for verification, wrapped in the second inscription envelope, as defined in [Inscription Metadata](oip-01-inscription-metadata.md).
2. Publisher SHOULD provide the purchaser with the following:
   * Rendered content of the purchased ordinal. This is typically an image, but may also be any content type supported by Ordinal Theory, including but not limited to text, movie, sound, etc.
   * Instruction to inscribe using the purchased address, and not any other addresses.&#x20;
3. Purchaser CAN inscribe the purchased inscription at any time.
4. Publisher SHOULD take note of sold Inscriptions and not to oversell.
5. Publisher SHOULD forward the commission from the fee collected by Purchaser, based on any contracts that Publisher with the Creator.
6. Sites and services or marketplaces that support verifiable ordinal SHOULD provide and indicator if an ordinal is a Verifiable Ordinal and SHOULD provide a checkmark if it passes or fails validation.&#x20;
   * If it fails, reason(s) on the failure SHOULD be provided.&#x20;

#### Signature and Verification

Referring to the example above, signature generation, by the publisher, can be generated as follows, using the standard `signmessage` command of Bitcoin Core:&#x20;

```bash
$ bitcoin-cli signmessage 13fh1atRp7rV2qdGuxhFabGkmKAnRia9Kq "eb9d4726a7caaaf5a7fe082059cb37b97b188845b70f476fa9833ec3079f5600 wiz-01 bc1qq0f8gndxqjftxalke9q9kxcgw6da7cp0vwflpj"
HzXmUa2mXuu7kCog7B7NqSR61Oa/Tr13IixoJG6C3e3qCdKptNL0pT0wxQ8oZGz9hvgjnq4WL+pMSjsqV+lVv94=
```

Verification can be done by anyone using `verifymessage` as follows:

```
$ bitcoin-cli verifymessage 13fh1atRp7rV2qdGuxhFabGkmKAnRia9Kq "HzXmUa2mXuu7kCog7B7NqSR61Oa/Tr13IixoJG6C3e3qCdKptNL0pT0wxQ8oZGz9hvgjnq4WL+pMSjsqV+lVv94=" "eb9d4726a7caaaf5a7fe082059cb37b97b188845b70f476fa9833ec3079f5600 wiz-01 bc1qq0f8gndxqjftxalke9q9kxcgw6da7cp0vwflpj"
true
```

Take note that verification must be carried out against the genesis inscription transaction, not the eventual or current owner where the inscription ordinal can be located.&#x20;

If `sri` is defined at collection, content integrity verification should also be carried out in accordance to [W3C's SRI specification](https://www.w3.org/TR/SRI/).&#x20;

### Improvements

Further improvements to OIP-2: Verifiable Ordinal Collection can include:

1. Multi-publisher workflow by allowing Publisher to publish sales on the blockchain.
   * This will allow for better tracking of `lim` beyond single Publisher in order not to oversell.
2. Traits-based inscriptions, where collection is defined as permutations of traits rather than defined as individual inscriptions.&#x20;

The above may be further published as separate proposals and is intended to not be included as part of this proposal for better focus and clarity.

### Alternatives

Exploring various alternative proposals that are in the similar space:

1. [Generative BRC-721](https://github.com/jerryfane/generative-brc-721)
   * This allows for collection defined via traits-based permutations.
   * While the traits are stored on-chain in a form of stringified data, the resulting artefact is a metadata with instructions on how to recreate the image.
   * This, however, would render on ordinals.com as text but not image and would require sites to specifically support the standard to generate the desired image.&#x20;
2. [BRC 721 experimental proposal](https://brc-721.gitbook.io/about-the-brc-721-experimental-proposal/)
   * This proposal introduces externally linked (IPFS) images.&#x20;
   * It does not adhere to [Ordinal Theory's definition of digital artifacts](https://docs.ordinals.com/inscriptions.html), which states that inscription content has to be entirely on-chain.
3. [BRC-721 Ordinals Collection Protocol](https://www.brc721.com)
   * This is a protocol that introduces a similar standard with that of ERC-721 of Ethereum.
   * Content is not on-chain and is externally linked in the form of `tokenURI`.&#x20;

### FAQ

#### 1. What tools are available to verify Verifiable Ordinals?&#x20;

Verification of verifiable ordinals can be carried out using standard tooling such as Bitcoin Core, using commands such as `verifymessage`.

Programming-language specific toolings and libraries are still being developed and will be updated as tools and libraries are known or available. If you have developed a tool, please submit a pull request to update this document with a link to your tools.

#### 2. Would this break Ordinals for other users/tools/sites that are not aware of Verifiable Ordinals?

Absolutely not. Verifiable Ordinals is backward-compatible. Services that are not aware of Verifiable Ordinals will simply be displaying ordinals and their inscriptions fine.




---
description: Enable a method for users to create and update their inscription lists
---

# OIP-05: On-Chain Renewable List (ORL)

### Overview & Motivation

Most marketplaces manage Ordinals collections through offline JSON files. Several on-chain collection protocols like brc-721, gbrc-721, brc69, etc., also exist, but they are mainly influenced by erc-721 and are not compatible with the existing Ordinals inscription system.

Our goal is to establish an open on-chain renewable collection protocol that seamlessly integrates with the existing Ordinals system. Creating and maintaining collections should not require any reliance on trusted third parties or place excessive burdens on maintainers.

### How It Works

Users can deploy and create collections, with the option to add or remove inscriptions as desired. It's also possible to reference inscriptions from other collections within a collection. The owner of deployed inscriptions is the only one authorized to modify the collection's contents, with ownership transfer functionality.

#### Deploy the Collection

- Inscribe the deploy inscription and transfer it to a Ordinals-compatible wallet with the desired parameters.

```
{
  "p": "ORL",
  "op": "deploy",
  "slug": "ordinals",
  "name": "Ordinals",
  "desc": "All Ordinals inscriptions on Bitcoin",
  "icon": "765cf24db22df4d7bae1cd12e5ee4780dc263486e426d8d1758eaa0515fa6fcei0",
  "max": "2100000000000000"
}
```

| Key  | Required? | Description                                                            |
| ---- | --------- | ---------------------------------------------------------------------- |
| p    | Yes       | Protocol: Helps other systems identify and process ORL events          |
| op   | Yes       | Operation: Type of event (Deploy, Add, Remove)                         |
| slug | Yes       | Slug: 1-256 letter identifier of the ORL (valid letter: `-_0-9a-zA-Z`) |
| name | Yes       | Name of this ORL collection                                            |
| desc | Yes       | Description of this ORL collection                                     |
| icon | Yes       | Icon's inscription ID of this ORL collection                           |
| max  | No        | Max supply: set max supply of the ORL, default to 2100000000000000     |


#### Modify the Collection

- Inscribe the add/remove function to your Ordinals compatible wallet with the desired parameters.
- Once received, send the inscription to yourself to enact the changes.

##### Add Inscriptions or Collections to ORL

Careful when using inscription service. Some tools first inscribe to themselves then forward it to the customer, potentially wasting the add/remove function. Some Ordinals wallets generate a new address each time, so ensure the inscription is sent to the address holding the deploy-inscription.

```
{
  "p": "ORL",
  "op": "add",
  "slug": "ordinals",
  "items": ["765cf24db22df4d7bae1cd12e5ee4780dc263486e426d8d1758eaa0515fa6fcei0", "tick2", ...]
}
```

| Key   | Required? | Description                                                            |
| ----- | --------- | ---------------------------------------------------------------------- |
| p     | Yes       | Protocol: Helps other systems identify and process ORL events          |
| op    | Yes       | Operation: Type of event (Deploy, Add, Remove)                         |
| slug  | Yes       | Slug: 1-256 letter identifier of the ORL (valid letter: `-_0-9a-zA-Z`) |
| items | Yes       | List of inscription ID of this ORL collection, or list of other tick   |

##### Remove Inscriptions or Collections from ORL

```
{
  "p": "ORL",
  "op": "remove",
  "slug": "ordinals",
  "items": ["765cf24db22df4d7bae1cd12e5ee4780dc263486e426d8d1758eaa0515fa6fcei0", "tick2", ...]
}
```

| Key   | Required? | Description                                                            |
| ----- | --------- | ---------------------------------------------------------------------- |
| p     | Yes       | Protocol: Helps other systems identify and process ORL events          |
| op    | Yes       | Operation: Type of event (Deploy, Add, Remove)                         |
| slug  | Yes       | Slug: 1-256 letter identifier of the ORL (valid letter: `-_0-9a-zA-Z`) |
| items | Yes       | List of inscription ID of this ORL collection, or list of other tick   |

### Notes

- Only the first deployer of a ticker can claim that ticker.
- The valid ticker must match the regexp pattern `[-_0-9a-zA-Z]{3,8}`.
- In case of events occurring in the same block, prioritization is determined by their confirmation order in the block (first to last).
- Similar to brc-20's transfer function, the add/remove function, once inscribed to the collection maintainer's address, needs to be sent to oneself to take effect.
- Each add/remove inscription can be used only once.
- The objects managed by the add/remove functions can be other inscriptions or even other collections. This implies that a collection includes not only directly added inscriptions but also real-time complete references to other collections.
- When adding or removing inscriptions or collections, the operations are independent. Changes to inscriptions do not affect the internal state of referenced collections. Referenced collections are always fully and instantly referenced.

### Contact Us

Please contact UniSat Team on our Discord / X(Twitter) to help us improve the proposal.




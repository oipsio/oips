---
description: Adding a recovery witness script to the Taproot ScriptTree
---

# OIP-03: Recoverable Commits

### Problem

Known issue - [https://github.com/ordinals/ord/issues/904](https://github.com/ordinals/ord/issues/904)

Bitcoin Ordinal Inscriptions are created using taproot addresses. Not a standard taproot address, but rather a segregated witness (script) based taproot address. Unlike the traditional legacy based addresses, which can be spent using the corresponding “private” key, SegWit addresses allow you to create non-key based accounts that can be unlocked using scripts. Both native and nested SegWit accounts in both legacy and Bech32 formats can only be linked to a single script. However, the most exciting thing about taproot addresses is that they utilize a ScriptTree, which enables you to have multiple TapLeafs, where each leaf can be linked to a different script, enabling for an array of redemption methods to be applied to a single address.

Unfortunately, despite utilizing ScriptTrees, the original implementation of inscriptions only uses a single TapLeaf, with a single method of redemption, which is the ORD inscription commit and reveal witness script. This script includes the full content of the item being inscribed, which is the attribute that causes the high fee for spending from that address. In order to create the inscription by spending from the address, the total fees required must be  covered by a single unspent transaction output (UTXO). Any other funds sent to that address that are less than the required amount can rarely be recovered and are often lost forever.

A simplified version of the original implementation of the bitcoin inscription commit and reveal address creation flow (in BitcoinJS format) can be seen below:

```
var witness_script = bitcoin.script.compile([
	tweaked_public_key,
	bitcoin.opcodes.OP_CHECKSIG,
	bitcoin.opcodes.OP_FALSE, // referred to as an ENVELOPE
	bitcoin.opcodes.OP_IF, // adding false first enables prunable content
	OP_PUSH("ord"), // specifies its an ordinal inscription
	OP_PUSH("text/plain;charset=utf-8"), // defines media type
	OP_PUSH("Hello World"), // media content
	bitcoin.opcodes.OP_ENDIF // end of ENVELOPE
]);
var script_tree = [{output: witness_script}];
var redeem_script = {output: witness_script, redeemVersion: 192};
var inscription = bitcoin.payments.p2tr({
	internalPubkey: sliced_public_key,
	scriptTree: script_tree,
	redeem: redeem_script,
	network: network
});
var commit_and_reveal_address = inscription.address;
```

### **Solution**

By introducing a second TapLeaf with a simplified “recovery” script that does not include the content that is trying to be inscribed, the fees can - when needed; be reduced and have the potential to then enable unsuitable UTXOs to be recovered. Since redeem scripts do not affect the address, when it comes to spending from the address at the reveal phase, it is possible to use either of the redeem scripts that match those found in the initial commit ScriptTree.

However, it is important to note that the initial commit address must have used the same ScriptTree that includes both options initially. You cannot change the ScriptTree between the commit (deposit) and reveal (inscription) phases without affecting the address.

Our proposed ScriptTree for recoverable commits is as follows:

```
var inscribe_script = bitcoin.script.compile([
	tweaked_public_key,
	bitcoin.opcodes.OP_CHECKSIG,
	bitcoin.opcodes.OP_FALSE, // referred to as an ENVELOPE
	bitcoin.opcodes.OP_IF, // adding false first enables prunable content
	OP_PUSH("ord"), // specifies its an ordinal inscription
	OP_PUSH("text/plain;charset=utf-8"), // defines media type
	OP_PUSH("Hello World"), // media content
	bitcoin.opcodes.OP_ENDIF // end of ENVELOPE
]);
var recover_script = bitcoin.script.compile([
	tweaked_public_key,
	bitcoin.opcodes.OP_CHECKSIG
]);
var script_tree = [{output: inscribe_script}, {output: recover_script}];
var inscribe = {output: inscribe_script, redeemVersion: 192};
var recover = {output: recover_script, redeemVersion: 192};
var inscription = bitcoin.payments.p2tr({
	internalPubkey: sliced_public_key,
	scriptTree: script_tree,
	redeem: inscribe, // this can be either inscribe or recover
	network: network
});
var recoverable_commit_and_reveal_address = inscription.address;
```

### Support

This feature is already supported by the [ORDIT SDK](https://sado.space/docs/ordit-introduction) - but do reach out if you know of any other tools or services that implement this standard whilst we wait for changes to ORD itself.

Please reach out on Twitter (@[m\_smalley](http://twitter.com/m\_smalley)) or visit [Sado Labs](https://sado.space/blog/taproot-recoverable-commits) for more details.

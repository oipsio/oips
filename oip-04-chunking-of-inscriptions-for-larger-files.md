# OIP-04: Chunking of Inscriptions for Larger Files

oip-04-chunking-of-inscriptions-for-larger-files

**Status:** Draft

## Overview

Given that Bitcoin's block size limitation stands at a maximum of 4MBs, users aiming to inscribe files larger than this limit are compelled to utilize recursive techniques. However, the present-day scenario lacks a uniform standard for identifying inscriptions with recursives.

The enhancement underlined in this proposal aims to establish a standard for embedding instructions with the recursive inscription. These instructions will aid in the "stitching" of recursive inscriptions, ensuring proper reassembly.

## Objectives

1. Implement a universally-accepted standard for identifying inscriptions using recursives.
2. Ensure proper reassembly of inscriptions by embedding comprehensive instructions.

## Proposed Solution

The solution encompasses metadata detailing the recursive inscriptions, and this metadata is inscribed following the OIP-01 standard.

### Flow:

1. The creator divides the file and inscribes these partial files.
2. The creator embeds the recursive inscription together with relevant metadata.
3. Viewers interpret and reconstruct the full inscription based on the provided metadata.

## Metadata Format for Recursive Inscription of Large Files

Outlined below is the JSON format employed for Metadata concerning large file inscriptions:

```json
{
  "p": "chunks",
  "v": 1,
  "mt": "",
  "iids": [
     // Array of inscription identifiers
  ],
  "meta": {
    // Meta data for the inscription
  }
}
```
### Notes:

1. Fields `p` (protocol) and `v` (version) are mandatory.
2. `mt` denotes the file type (eg: image/webp)
3. `iids` is an array storing identifiers of inscriptions used as recursives.
4. `meta` encapsulates metadata for the inscription. Eg: grid, width, height can be applied for images.

## Example of metadata of Large Images Inscription using recursives

The following example is a recursive inscription of 4 webp images in a grid of 2x2

```json
{
  "p": "chunks",
  "v": 1,
  "mt": "image/webp",
  "iids": [
    "c16889125e190eb170d54c66144826f4b72c113a9fe6c371c70e54b75841fde3i0",
    "b62a24a148d05dbe369e44a17567739c9c7ec4000bad9256b1551f641a17a27fi0",
    "79db8d3030fd594e4154aeb23417ce8e695ca0fac3d1d1c2e08adbe096ee5075i0",
    "e18aeeb16c55fdde73bd6b07ccdbe725fe2954b6ed18705a362f12b1bc5c3a5ei0"
  ],
  "meta": {
    "grid": "2x2",
    "width": 1024,
    "height": 1024
  }
}
```


### Tools Incorporating OIP-4

#### 1. Large Images Inscription:

The [Ordlify Large Image Inscriptions tool](https://ordlify.com/recursives/image) is designed to inscribe sizable images by segmenting and then reassembling them per the OIP-04 standard.

### Support & Inquiries
For additional insights and support, do reach out on Twitter via [@afxal](https://x.com/afxal) or [@shafiu](https://x.com/shafiu).
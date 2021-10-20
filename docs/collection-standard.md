---
sidebar_position: 1
---

# Collection Metadata Standard

## Collection Metadata program

The concept of the Collection Metadata program is to provide decorator structs to a collection token. Basic info about the collection is provided with the `Collection` struct, whose account address is a PDA with a derived key of `['collection', collection_id]`.

The NFT Token Standard `Metadata` struct is extended with a key `collection` which points to a map with the following fields:

| Field     | Type  | Description                                                                | Display suggestions                               | 
| --------- | ----- | -------------------------------------------------------------------------- | ------------------------------------------------- | 
| assets    | array | append only array of NFT addresses                                         | single NFT view, resolving links to specific NFTs |
| member_of | array | array of Membership Maps displaying the collections the NFT is a member of | single NFT view, resolving links to specific NFTs |

#### Membership Map

| Field     | Type   | Description                                 | Display suggestions                                                                | 
| --------- | ------ | ------------------------------------------- | ---------------------------------------------------------------------------------- | 
| address   | string | collection address                          | single NFT view, links to collection and resolved collection name                  |
| signature | string | a hash of the NFT, signed by the collection | single NFT view, resolves collection public key and verifies validity of signature |

### Ownership

* A collection is an NFT following the NFT Token Standard. It can be transferred, sold, fractionalized and any other behavior allowed for a Solana NFT. Collections can themselves belong to collections. Collections can mint tokens and collections, both of which will automatically be signed and added to the minting collection. 

* There is nothing stopping a collection from claiming an arbitrary NFT is a member of the collection, and showing it in its asset array. Therefore, you should always verify the signature in the asset NFTs metadata. A minting collection will automatically add itself and it's signature to asset NFTs as they are created. Unrelated NFTs won't show the collection address and signature unless they belong to the collection. NFTs that choose to no longer associate with the collection may remove the `collection.member_of` array value that references the collection to be removed.

* The collection's asset array is append only. Assets can be added to collections, but can not be removed. 

* NFTs don't have to be minted by the collection, and can be added later.

### JSON Structure

```json
{
  "name": "Pigs on Solana Season #1",
  "symbol": "",
  "description": "Sub-collection for Pigs on Solana. Represents the first season released.",
  "seller_fee_basis_points": 0,
  "image": "",
  "animation_url": "",
  "external_url": "https://pigsonsolana.example.com/season/1",
  "collection": {
    "assets": ["BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh211111111", "BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh211111112"],
    "member_of": [
      {
        "address": "BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh200000000",
        "signature": "<signature in hex>"
      }
    ]
  }
}
```
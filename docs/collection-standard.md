---
sidebar_position: 1
---

# Collection Metadata Standard

## Collection Metadata program

The concept of the Collection Metadata program is to provide decorator structs to a collection token. Basic info about the collection is provided with the `Collection` struct, whose account address is a PDA with a derived key of `['collection', collection_id]`.

Your wallet should be using the following information from the on-chain metadata. The [NFT Token Standard extension section](#nft-token-standard-extension) will explain how.

| Field       | Type                                     | Description                                                                                             | 
| ----------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------- | 
| name        | string                                   | name of the collection                                                                                  | 
| description | string                                   | short description of the collection                                                                     |
| members     | array<address>                           | an array of address of tokens or sub-collections belonging to this collection                           |
| member_of   | array<[Membership Map](#membership-map)> | array of [Membership Map](#membership-map) displaying the collections the sub-collection is a member of |


## NFT Token Standard extension

The NFT Token Standard `Metadata` struct is extended with a key `collection` which points to a map with the following fields:

| Field     | Type                                     | Description                                                                                   | Display suggestions                               | 
| --------- | ---------------------------------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------------- | 
| member_of | array<[Membership Map](#membership-map)> | array of [Membership Map](#membership-map) displaying the collections the NFT is a member of  | single NFT view, resolving links to specific NFTs |

### Membership Map

| Field     | Type   | Description                                 | Display suggestions                                                                     | 
| --------- | ------ | ------------------------------------------- | --------------------------------------------------------------------------------------- | 
| address   | string | collection address                          | single NFT view, links to collection and resolved collection name                       |
| signature | string | a hash of the NFT, signed by the collection | single NFT view, resolves with collection public key and verifies validity of signature |

## Ownership

* A collection is a book-keeping record in the Metaplex ecosystem. Collections can themselves belong to collections. Collections can mint tokens and collections, both of which will automatically be signed and added to the minting collection.

### Minting new members

* Collections can mint NFTs or sub-collections, and those 

* The collection's asset array is append only. Assets can be added to collections, but can not be removed.
* NFTs don't have to be minted by the collection, and can be added later.

### Adding existing NFTs

* NFTs don't have to be minted by the collection, and can be added later.
* A collection would append the NFT it is preparing to add to it `members` array.
* After the NFT address has been added to the `members` array, the NFT can call a function on the Collection to receive the verifying signature. The Collection will check for the address in the array before the signature is returned.

### Concerns / Considerations

* There is nothing stopping a collection from claiming an arbitrary NFT is a member of the collection, and showing it in its asset array. Therefore, you should always verify the signature in the asset NFTs metadata.
* Unrelated NFTs won't show the collection address and signature unless they belong to the collection.
* NFTs that choose to no longer associate with the collection may remove the `collection.member_of` array value that references the collection to be removed.
* It is highly recommended that the Collections use an append-only `members` array. This will give confidence against bait-ands-switch tactics and devaluing NFTs sold under certain conditions. It is expected that the value of a Collection that uses an append-only array will be higher than those that do not.
* Members of sub-collections are also considered members of the parent collections.

### JSON Structure example

```json
{
  "name": "Parent Collection",
  "description": "Top level example collection",
  "members": ["BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh200000001"]
  
}
```

```json
{
  "name": "Middle Collection",
  "description": "Example sub-collection",
  "members": ["BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh200000002"],
  "member_of": ["BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh200000000"]
}
```

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
    "member_of": [
      {
        "address": "BxHJqGtC629c55swCqWXFGA2rRF1igbbTmh200000001",
        "signature": "<signature in hex>"
      }
    ]
  }
}
```
# Response Examples

**@TODO:** write about general response structure here

**@TODO:** note that createdAt & updatedAt are omitted

## Client

```json
{
  "gasAllowance": "40500000",
  "email": "email@example.com",
  "name": "My Codex Application",
  "address": "0x627306090abab3a6e1400e9345bc60c78a8bef57"
}
```

Property                      | Description
----------------------------- | -----------
name                          | The name of the application. This will be shown in the Codex Viewer as a registered application, taking the place of the Ethereum address in the Codex Record’s provenance section.
email                         | The application developer’s email address. This will be used to communicate any breaking API changes or development updates.
address                       | The Ethereum address that identifies the application, provisioned and managed by Codex behalf of the application developer.
gasAllowance                  | The amount of gas left that the application can spend until it resets. See the [Gas Allowance](#gas-allowance) section for details.


## Codex Record

```javascript
{
  "tokenId": "0",
  "isPrivate": false,
  "isIgnored": false,
  "isInGallery": false,
  "providerId": "codex",
  "approvedAddress": null,
  "isHistoricalProvenancePrivate": true,
  "providerMetadataId": "5bae56f75d7971becf854a72",
  "ownerAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "nameHash": "0x4955c7fd1cb2de006320e77b157409b84ab2df69d9c0720f609d7a17dd2a674c",
  "descriptionHash": "0xcb7ab38fbc9919a3b04eabc343d7e649335ad748213df20e65d1979d0d3aa800",
  "fileHashes": [
    "0xb39e1bbc6d50670aafa88955217ddf1e9e1f635a2cfc8d5d806a730f014c7210"
  ],
  "provenance": [
    {
      // see " Provenance Entry" below
    }
  ],
  "metadata": {
    // see "File" below
  },
  "provider": {
    // see "Provider" below
  }
}
```

Property                      | Description
----------------------------- | -----------
tokenId                       | The unique identifier of this Codex Record
metadata                      | Will `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address. See [Metadata](#metadata) for details.
provider                      | See [Metadata Provider](#metadata-provider) for details.
nameHash                      | The hash of the metadata's plain text name.
isIgnored                     | This flag indicates that a user has chosen to "ignore" an incoming Codex Record transfer. This is useful to hide incoming transfers in a User Interface since there's no blockchain mechanism to explicitly reject a transfer.
isPrivate                     | This flag indicates that the metadata for this record is private and can only be retrieved by the owner, the `approvedAddress`, and the
provenance                    | An array of [Provenance Events](#provenance-event).
fileHashes                    | An array of file hashes that belong to this Codex Record's metadata.
providerId                    | See unique id of the [Metadata Provider](#metadata-provider).
ownerAddress                  | The Ethereum address of the client / user that currently owns this Codex Record.
descriptionHash               | The hash of the metadata's plain text description.
approvedAddress               | The Ethereum address of the user currently approved to accept the transfer of this Codex Record. See [Transferring a Codex Record](#transferring-a-codex-record) for details. Approved addresses are considered part of the `whitelistedAddresses` array.
providerMetadataId            | The unique ID of the metadata that belongs to this Codex Record.
whitelistedAddresses          | An array of Ethereum addresses allowed to view all private metadata for this Codex Record. This allows users to give people read-only access to their Codex Records. Will be `undefined` if you are not the owner.
isHistoricalProvenancePrivate | This flag indicates whether or not [historical provenance](#historical-provenance) (i.e. `metadata.files`) should be hidden, regardless of the value of `isPrivate`.

<aside class="notice">
  <code>nameHash</code>, <code>descriptionHash</code>, and <code>fileHashes</code> can be used to verify the data sent from a metadata provider (e.g. The Codex API) matches the information on the blockchain, and therefore has not been tampered with.
</aside>


## Metadata

```javascript
{
  "codexRecordTokenId": "0",
  "hasPendingUpdates": false,
  "id": "5bae56f75d7971becf854a72",
  "name": "Really Cool Codex Record",
  "description": "This is a really cool Codex Record!",
  "creatorAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "nameHash": "0x4955c7fd1cb2de006320e77b157409b84ab2df69d9c0720f609d7a17dd2a674c",
  "descriptionHash": "0xcb7ab38fbc9919a3b04eabc343d7e649335ad748213df20e65d1979d0d3aa800",
  "fileHashes": [
    "0xb39e1bbc6d50670aafa88955217ddf1e9e1f635a2cfc8d5d806a730f014c7210"
  ],
  "mainImage": {
    // see "File" below
  },
  "files": [
    {
      // see "File" below
    }
  ],
  "images": [
    {
      // see "File" below
    }
  ]
}
```

**@TODO:** Explain metadata here

Property           | Description
------------------ | -----------
id                 | The unique ID of the metadata.
name               | The plain text name of the Codex Record. Will `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address.
files              | An array of supplemental files that belong to this metadata. This is considered the "[historical provenance](#historical-provenance)". Can `undefined` if `isHistoricalProvenancePrivate` is `true` on the Codex Record. See [File](#file) for details.
images             | An array of supplemental images that belong to this metadata. Will `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address. See [File](#file) for details.
nameHash           | The hash of the plain text name.
mainImage          | The main image of this Codex Record. Will `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address.
fileHashes         | An array of file hashes that belong to this metadata.
description        | The plain text description of the Codex Record. Will `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address.
creatorAddress     | The Ethereum address of the client / user who created this metadata (not necessarily the current Codex Record owner!)
descriptionHash    | The hash of the plain text description.
hasPendingUpdates  | This flag is true if there is currently a "modify" transaction processing on the blockchain (e.g. after you [modify an existing record](#modify-an-existing-record)). This is useful for showing some sort of loading state on a Codex Record while modifications are pending.
codexRecordTokenId | The `tokenId` of the Codex Record this metadata belongs to.


## Provenance Event

```json
{
  "type": "transferred",
  "oldOwnerAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "newOwnerAddress": "0xf17f52151ebef6c7334fad080c5704d77216b732",
  "transactionHash": "0x0a3a518d1a0786cbdc6265dc24fc0112411c996363465a2789dc3d8021cd4c44",
}
```

**@TODO:** Explain provenance events here

Property        | Description
--------------- | -----------
type            | One of `['created', 'modified', 'transferred']`.
oldOwnerAddress | The Ethereum address of the owner before this transaction occurred. For `created` events, this will be the [zero address](#zero-address). For `modified` events, this will be the same as `newOwnerAddress`.
newOwnerAddress | The Ethereum address of the after before this transaction occurred. For `modified` events, this will be the same as `oldOwnerAddress`.
transactionHash | The Ethereum transaction hash of the transaction that emitted this event.


## Metadata Provider

```json
{
  "id": "codex",
  "description": "The Codex API",
  "metadataUrl": "https://rinkeby-codex-registry-api.codexprotocol.com/v1/records/"
}
```

**@TODO:** Explain what a metadata provider is here

Property    | Description
----------- | -----------
id          | The unique ID of the metadata provider.
description | A description of the metadata provider.
metadataUrl | The URL to request Codex Record Metadata from. The Codex Record's `tokenId` should be appended to this URL to retrieve metadata about that record.


## File

```json
{
  "width": "1024",
  "size": "181516",
  "height": "1024",
  "fileType": "image",
  "name": "cool-file.jpg",
  "mimeType": "image/jpeg",
  "id": "5bae56f65d7971becf854a71",
  "creatorAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "hash": "0xb39e1bbc6d50670aafa88955217ddf1e9e1f635a2cfc8d5d806a730f014c7210",
  "uri": "https://s3.amazonaws.com/codex.registry/files/1538152180696.c8a9ec69-0ed8-4516-a34c-e15ebbe4749c.jpg"
}
```

**@TODO:** Explain files here

Property       | Description
-------------- | -----------
id             | The unique ID of the file.
uri            | The URL of the file.
size           | The size (in bytes) of the file.
name           | The name of the file, as passed in [when created](#create-a-codex-record).
hash           | The hash of the file data.
width          | The width (in pixels) of the file, if it is an image. Otherwise `null`.
height         | The height (in pixels) of the file, if it is an image. Otherwise `null`.
fileType       | One of `['file', 'video', or 'document']`. Useful when displaying this file on a web page.
mimeType       | The [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) of the file.
creatorAddress | The Ethereum address of the client / user created this file (not necessarily the current Codex Record owner!)

# Response Examples

> An example success response (from a [Get Client](#get-client) request):

```javascript
{
  "error": null,
  "result": {
    "email": "email@example.com",
    "name": "My Codex Application",
    "faucetDripLastRequestedAt": null,
    "codxBalance": "10000000000000000000",
    "faucetDripNextRequestAt": "2018-11-26T19:25:03.056Z"
    "address": "0x627306090abab3a6e1400e9345bc60c78a8bef57"
  }
}
```

All responses from The Codex API will have the same structure, regardless
of whether or not the request was successful. The Codex API will _always_
return `Content-Type: application/json; charset=utf-8` response bodies with an
object containing the keys `error` and `result`, one of which will always be
`null`. Always returning a JSON body ensures you can always reliably parse
responses from The Codex API as JSON, even if there's an error.

If there was error, `result` will be `null` and the error details can be found
in the `error` object. The `error` object has two keys, `code` and `message`.
`error.code` will be the same as the HTTP status header on the response, and
`error.message` will provide an explanation of what went wrong.

Similarly, if the request was successful, `error` will be `null` and the
requested data can be found in the `response` object.

<aside class="notice">
  Just about every object in response data will have <code>createdAt</code> and
  <code>updatedAt</code> <a href="https://en.wikipedia.org/wiki/ISO_8601" rel="noopener noreferrer">ISO 8601</a>
  timestamps. However, all examples in this section have had these values
  omitted for the sake of brevity.
</aside>

> An example error response (expired access token):

```javascript
{
  "error": {
    "code": 401,
    "message": "Invalid token: access token has expired"
  },
  "result": null
}
```

## Client

```javascript
{
  "email": "email@example.com",
  "name": "My Codex Application",
  "faucetDripLastRequestedAt": null,
  "codxBalance": "10000000000000000000",
  "faucetDripNextRequestAt": "2018-11-26T19:25:03.056Z"
  "address": "0x627306090abab3a6e1400e9345bc60c78a8bef57"
}
```

Property                  | Type   | Description
------------------------- | ------ | ---------------------------------------
name                      | String | The name of the application. This will be shown in the Codex Viewer as a registered application, taking the place of your application's Ethereum address in the Codex Record’s provenance.
email                     | String | The application developer’s email address. This will be used to communicate any breaking API changes or development updates.
address                   | String | The Ethereum address that identifies the application, provisioned and managed by Codex behalf of the application developer.
codxBalance               | String | The amount of CODX your application has remaining to spend on transactions. See [CODX Tokens & Fees](#codx-and-fees) for details.
faucetDripNextRequestAt   | Date   | _(Testnets only)_ The time at which your application will next be able to [request CODX from the faucet](#get-codx-from-faucet).
faucetDripLastRequestedAt | Date   | _(Testnets only)_ The time at which your application last [requested CODX from the faucet](#get-codx-from-faucet).


## OAuth2 Access Token

```javascript
{
  "accessTokenExpiresAt": "2018-10-02T22:36:57.808Z",
  "accessToken": "d49694e5a3459759cc7ac1741de246e184e51d6e"
}
```

Property             | Type   | Description
-------------------- | ------ | ------------------------------------------------
accessTokenExpiresAt | Date   | The date at which this access token will expire. See [Access Token Expiration](#access-token-expiration) for details.
accessToken          | String | The access token itself. This should be [added to the Authorization header](#making-authenticated-requests) of all requests made to The Codex API.



## Codex Record

```javascript
{
  "tokenId": "0",
  "isPrivate": false,
  "isIgnored": false,
  "isInGallery": false,
  "providerId": "codex",
  "isHistoricalProvenancePrivate": true,
  "providerMetadataId": "5bae56f75d7971becf854a72",
  "ownerAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "approvedAddress": "0x821aea9a577a9b44299b9c15c88cf3087f3b5544",
  "nameHash": "0x4955c7fd1cb2de006320e77b157409b84ab2df69d9c0720f609d7a17dd2a674c",
  "descriptionHash": "0xcb7ab38fbc9919a3b04eabc343d7e649335ad748213df20e65d1979d0d3aa800",
  "whitelistedAddresses": [
    "0xf17f52151ebef6c7334fad080c5704d77216b732",
    "0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef"
  ],
  "whitelistedEmails": [
    "user@example.com"
  ],
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

Property                      | Type                                         | Permissions                                      | Description
----------------------------- | -------------------------------------------- | ------------------------------------------------ | -----------
tokenId                       | String                                       | Public                                           | The unique identifier of this Codex Record
metadata                      | [Metadata](#metadata)                        | Whitelisted Addresses (if `isPrivate` is `true`) | Will be `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address. See [Metadata](#metadata) for details.
provider                      | [Metadata Provider](#metadata-provider)      | Public                                           | See [Metadata Provider](#metadata-provider) for details.
nameHash                      | String                                       | Public                                           | The hash of the metadata's plain text name.
isIgnored                     | Boolean                                      | Public                                           | This flag indicates that a user has chosen to "ignore" an incoming Codex Record transfer. This is useful to hide incoming transfers in a User Interface since there's no blockchain mechanism to explicitly reject a transfer.
isPrivate                     | Boolean                                      | Public                                           | This flag indicates that the metadata for this record is private and can only be retrieved by the owner, the `approvedAddress`, and the addresses listed in `whitelistedAddresses` / `whitelistedEmails`.
provenance                    | Array[[Provenance Event](#provenance-event)] | Public                                           | An array of [Provenance Events](#provenance-event).
fileHashes                    | Array[String]                                | Public                                           | An array of file hashes that belong to this Codex Record's metadata.
providerId                    | String                                       | Public                                           | See unique id of the [Metadata Provider](#metadata-provider).
ownerAddress                  | String                                       | Public                                           | The Ethereum address of the client / user that currently owns this Codex Record.
descriptionHash               | String                                       | Public                                           | The hash of the metadata's plain text description.
approvedAddress               | String                                       | Public                                           | The Ethereum address of the user currently approved to accept the transfer of this Codex Record. See [Transferring a Codex Record](#transferring-codex-records) for details. Approved addresses are considered part of the `whitelistedAddresses` array. See [Approved Addresses](#approved-addresses) for details.
providerMetadataId            | String                                       | Public                                           | The unique ID of the metadata that belongs to this Codex Record.
whitelistedEmails             | Array[String]                                | Owner Only                                       | An array of email addresses allowed to view private metadata for this Codex Record. This allows users to give people read-only access to their Codex Records. Will be `undefined` if you are not the owner. See [Whitelisted Addresses](#whitelisted-addresses) for details.
whitelistedAddresses          | Array[String]                                | Owner Only                                       | An array of Ethereum addresses allowed to view private metadata for this Codex Record. This allows users to give people read-only access to their Codex Records. Will be `undefined` if you are not the owner. See [Whitelisted Addresses](#whitelisted-addresses) for details.
isHistoricalProvenancePrivate | Boolean                                      | Public                                           | This flag indicates whether or not [historical provenance](#historical-provenance) (i.e. `metadata.files`) should be hidden, regardless of the value of `isPrivate`.

<aside class="notice">
  <code>nameHash</code>, <code>descriptionHash</code>, and <code>fileHashes</code> can be used to verify the data sent from a metadata provider (e.g. The Codex API) matches the information on the blockchain, and therefore has not been tampered with.
</aside>


## Metadata

```javascript
{
  "codexRecordTokenId": "0",
  "hasPendingUpdates": true,
  "id": "5bae56f75d7971becf854a72",
  "name": "Really Cool Codex Record",
  "description": "This is a really cool Codex Record!",
  "creatorAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "nameHash": "0x4955c7fd1cb2de006320e77b157409b84ab2df69d9c0720f609d7a17dd2a674c",
  "descriptionHash": "0xcb7ab38fbc9919a3b04eabc343d7e649335ad748213df20e65d1979d0d3aa800",
  "fileHashes": [
    "0xb39e1bbc6d50670aafa88955217ddf1e9e1f635a2cfc8d5d806a730f014c7210"
  ],
  "pendingUpdates": [
    {
      // see "Pending Update" below
    }
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

Metadata makes up all of the "[off-chain](#quot-off-chain-quot-vs-quot-on-chain-quot-data)"
data associated with a Codex Record, e.g. the "plain text" values for the
corresponding hashes stored on the blockchain. Storing this (potentially)
sensitive data off-chain is necessary for privacy controls, since the nature of
blockchain transactions are public.

Property           | Type                                     | Permissions                                                                                  | Description
------------------ | ---------------------------------------- | -------------------------------------------------------------------------------------------- | ----------------
id                 | String                                   | Public                                                                                          | The unique ID of the metadata.
name               | String                                   | Whitelisted Addresses (if `isPrivate` is `true`)                                                | The plain text name of the Codex Record. Will be `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address.
files              | Array[[File](#file)]                     | Whitelisted Addresses (if `isPrivate` is `true` and `isHistoricalProvenancePrivate` is `false`) | An array of supplemental files that belong to this metadata. This is considered the "[historical provenance](#historical-provenance)". Can `undefined` if `isHistoricalProvenancePrivate` is `true` on the Codex Record. See [File](#file) for details.
images             | Array[[File](#file)]                     | Whitelisted Addresses (if `isPrivate` is `true`)                                                | An array of supplemental images that belong to this metadata. Will be `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address. See [File](#file) for details.
nameHash           | String                                   | Public                                                                                          | The hash of the plain text name.
mainImage          | [File](#file)                            | Whitelisted Addresses (if `isPrivate` is `true`)                                                | The main image of this Codex Record. Will be `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address.
fileHashes         | Array[String]                            | Public                                                                                          | An array of file hashes that belong to this metadata.
description        | String                                   | Whitelisted Addresses (if `isPrivate` is `true`)                                                | The plain text description of the Codex Record. Will be `undefined` if `isPrivate` is `true` and you are not the owner or a whitelisted address.
pendingUpdates     | Array[[Pending Update](#pending-update)] | Owner Only                                                                                      | An array of updates that are currently being processed for this metadata. See [Pending Update](#pending-update) for details. Will be `undefined` if you are not the owner.
creatorAddress     | String                                   | Public                                                                                          | The Ethereum address of the client / user who created this metadata (not necessarily the current Codex Record owner!)
descriptionHash    | String                                   | Public                                                                                          | The hash of the plain text description.
hasPendingUpdates  | Boolean                                  | Public                                                                                          | This flag is true if there is currently a "modify" transaction processing on the blockchain (e.g. after [modifying a Codex Record's metadata](#modify-a-codex-record-39-s-metadata)). This is useful for showing some sort of loading state on a Codex Record while modifications are pending. See [Pending Update](#pending-update) for details.
codexRecordTokenId | String                                   | Public                                                                                          | The `tokenId` of the Codex Record this metadata belongs to.


## Pending Update

```javascript
{
  "id": "5bb661ab92d258fdf2efa29b",
  "name": "Super Duper New Title",
  "description": "This will replace the current description!",
  "nameHash": "0x444df25b54a53b842bb2af218e79e8cd2f1efca5c8f7a439451686fb3b825636",
  "descriptionHash": "0xcb7ab38fbc9919a3b04eabc343d7e649335ad748213df20e65d1979d0d3aa800",
  "fileHashes": [
    "0xd7bbfd8fd047f48c6724adb0633059f00f55339ed2fc96a529ef606d6ec76593"
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
  ],
}
```

Typically generated after [modifying a Codex Record's metadata](#modify-a-codex-record-39-s-metadata),
a Pending Update represents the new state metadata should have after a "modify"
transaction has been mined on the blockchain. This process is necessary due to
the asynchronous nature of blockchain transactions.

<aside class="success">
  Since a Pending Update is essentially a "placeholder" for a Codex Record's
  metadata, the structure is essentially the same.
</aside>

Property        | Type                 | Description
--------------- | -------------------- | ---------------------------------------
id              | String               | The unique ID of the pending update.
name            | String               | The new plain text name.
files           | Array[[File](#file)] | The new array of supplemental files.
images          | Array[[File](#file)] | The new array of supplemental images.
nameHash        | String               | The hash of the new plain text name.
mainImage       | [File](#file)        | The new main image.
fileHashes      | Array[String]        | An array of file hashes representing all the new files & images.
description     | String               | The new plain text description.
descriptionHash | String               | The hash of the new plain text description.


## Provenance Event

```javascript
{
  "type": "transferred",
  "oldOwnerAddress": "0x627306090abab3a6e1400e9345bc60c78a8bef57",
  "newOwnerAddress": "0xf17f52151ebef6c7334fad080c5704d77216b732",
  "transactionHash": "0x0a3a518d1a0786cbdc6265dc24fc0112411c996363465a2789dc3d8021cd4c44",
}
```

A Provenance Event is a record of a blockchain transaction related to a specific
Codex Record. A Codex Record's `provenance` array is essentially a transaction
ledger, detailing when it was created, who it was transferred to, and who
modified what data.

Property        | Type   | Description
--------------- | ------ | -----------------------------------------------------
type            | String | One of `['created', 'modified', 'transferred']`.
oldOwnerAddress | String | The Ethereum address of the owner before this transaction occurred. For `created` events, this will be the "zero address" (a special address that nobody can own, used as a "null value" for address-type variables.) For `modified` events, this will be the same as `newOwnerAddress`.
newOwnerAddress | String | The Ethereum address of the after before this transaction occurred. For `modified` events, this will be the same as `oldOwnerAddress`.
transactionHash | String | The Ethereum transaction hash of the transaction that emitted this event.


## Metadata Provider

```javascript
{
  "id": "codex",
  "description": "The Codex API",
  "metadataUrl": "https://rinkeby-api.codexprotocol.com/v1/records/"
}
```

A Metadata Provider is known source of Codex Record metadata. Since metadata
_must_ be stored "[off-chain](#quot-off-chain-quot-vs-quot-on-chain-quot-data)",
Metadata Providers are essentially databases / API endpoints which can be used
to retrieve Codex Record metadata.

<aside class="success">
  Currently Codex Protocol is the only metadata provider, but this functionality
  has been implemented to support the possibility of having third-party metadata
  hosts in the future.
</aside>

Property    | Type   | Description
----------- | ------ | ---------------------------------------------------------
id          | String | The unique ID of the metadata provider.
description | String | A description of the metadata provider.
metadataUrl | String | The URL to request Codex Record Metadata from. The Codex Record's `tokenId` should be appended to this URL to retrieve metadata about that record.


## File

```javascript
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

When files are uploaded to The Codex API, they are processed to determine
useful bits of information such as width, height, and size (in bytes). The are
then uploaded to [S3](https://aws.amazon.com/s3/), and the resulting information
is stored in the The Codex API database.

Property       | Type   | Description
-------------- | ------ | ------------------------------------------------------
id             | String | The unique ID of the file.
uri            | String | The URL of the file.
size           | String | The size (in bytes) of the file.
name           | String | The name of the file, as passed in [when created](#create-a-codex-record).
hash           | String | The hash of the file data.
width          | String | The width (in pixels) of the file, if it is an image. Otherwise `null`.
height         | String | The height (in pixels) of the file, if it is an image. Otherwise `null`.
fileType       | String | One of `['file', 'video', or 'document']`. Useful when displaying this file on a web page.
mimeType       | String | The [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) of the file.
creatorAddress | String | The Ethereum address of the client / user created this file (not necessarily the current Codex Record owner!)

<aside class="notice">
  Your application will likely only ever use the <code>fileType</code> and
  <code>uri</code> fields.
</aside>

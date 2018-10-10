# General Concepts

This section explains miscellaneous concepts referenced throughout this
documentation.


## API Requests

> This HTTP request:

```http
PUT /v1/client/record/100/metadata?name=Really%20Cool%20Codex%20Record HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Host: https://rinkeby-codex-registry-api.codexprotocol.com
Authorization: Bearer d49694e5a3459759cc7ac1741de246e184e51d6e

description=This+is+a+really+cool+Codex+Record!
```

> is handled identically to this one:

```http
PUT /v1/client/record/0/metadata HTTP/1.1
Content-Type: application/json
Host: https://rinkeby-codex-registry-api.codexprotocol.com
Authorization: Bearer e933e3e02e4f16d552e469367b25293d75c3d3c2

{
  "name": "Really Cool Codex Record",
  "description": "This is a really cool Codex Record!"
}
```

> and this one:

```http
PUT /v1/client/record/0/metadata?description=This%20is%20a%20really%20cool%20Codex%20Record! HTTP/1.1
Host: https://rinkeby-codex-registry-api.codexprotocol.com
Authorization: Bearer e933e3e02e4f16d552e469367b25293d75c3d3c2
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="name"

Really Cool Codex Record
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

The Codex API is very flexible with how it handles incoming requests. For most
routes, the API will accept a request with a `Content-Type` of
`multipart/form-data`, `application/x-www-form-urlencoded`, or
`application/json`. Additionally, you may specify request parameters as either
query parameters or body parameters (with the exception of file data, of course).

The exceptions are:

1. Anytime you send file data with the request, you must send the request as
   `Content-Type: multipart/form-data`.

1. When requesting access tokens, you must send the request as
   `Content-Type: application/x-www-form-urlencoded`. This is required by the
   [OAuth2 Specification](https://tools.ietf.org/html/rfc6749?#section-4.1.3).

For details on what to expect from The Codex API in response, see [Response Examples](#response-examples).

## Webhooks

Calling certain routes may cause asynchronous transactions to be submitted to
the blockchain. Specifically, any action that modifies "[on-chain](#quot-off-chain-quot-vs-quot-on-chain-quot-data)"
data is asynchronous and has an associated webhook event.

Your application should not assume the result of calling an asynchronous route
will be performed immediately. Instead, your application should have an endpoint
that can be called by The Codex API when the asynchronous action is complete and
the blockchain state has been updated.

For example, when updating [modifying a Codex Record's Metadata](#modify-a-codex-record-39-s-metadata),
your application should wait until it's webhook is called with the
`codex-record:modified` event. At this point your application can react to the
updated data in any way necessary.

### Asynchronous Actions

The following actions should be considered asynchronous, and your application
should wait for their associated webhook events before considering requests sent
to them as successful.

<aside class="success">
  Any action that modifies "<a href="#quot-off-chain-quot-vs-quot-on-chain-quot-data">on-chain</a>"
  data is an asynchronous action.
</aside>

Action                                                                      | Event(s)
--------------------------------------------------------------------------- | --------
[Creating a Codex Record](#create-a-codex-record)                           | `codex-record:created`
[Modifying a Codex Record's Metadata](#modify-a-codex-record-39-s-metadata) | `codex-record:modified`
[Starting a Transfer](#start-a-transfer)                                    | `codex-record:address-approved:owner` and `codex-record:address-approved:approved`
[Canceling a Transfer](#cancel-a-transfer)                                  | N/A
[Accepting a Transfer](#accept-a-transfer)                                  | `codex-record:transferred:old-owner` and `codex-record:transferred:new-owner`

### Webhook Structure

> An example request body sent to your application's webhook:

```javascript
{
  "timestamp": 1539024178127,
  "hash": "5fed9779c702a873e8c68872eca959876d9d00a84a1cb5fb58ecd6c3e83f307b",
  "eventName": "codex-record:created",
  "eventData": {
    // varies by event, usually a Codex Record
  }
}
```

When blockchain events occur, your application's webhook will be sent a `POST`
request with an `application/json` body containing the following data:

Property  | Type   | Description
--------- | ------ | -----------------------
hash      | String | The [sha256](https://nodejs.org/api/crypto.html#crypto_crypto_createhmac_algorithm_key_options) hash of the string `${timestamp}:${client_id}`, using your application's secret as the `key`. See [Verifying Webhook Requests](#verifying-webhook-requests) for details.
timestamp | Number | Unix timestamp (in milliseconds) of when the request was sent. See [Verifying Webhook Requests](#verifying-webhook-requests) for details.
eventName | String | The name of the event. See [Webhook Events](#webhook-events) for details.
eventData | Varies | The relevant data for this event. Usually a [Codex Record](#codex-record), but varies by event. See [Webhook Events](#webhook-events) for details.

<aside class="warning">
  Your webook endpoint <em>must</em> return a 2xx status code, or The Codex API will
  continue to retry the request several times (with exponential backoff) before
  giving up.
</aside>

### Webhook Events

The following table lists all webhook events.

Event Name                             | Event Data                    | Description
-------------------------------------- | ----------------------------- | -----------
codex-record:created                   | [Codex Record](#codex-record) | Sent after [creating a Codex Record](#create-a-codex-record), when the Codex Record has been created and logged on the blockchain.
codex-record:modified                  | [Codex Record](#codex-record) | Sent after [modifying a Codex Record's metadata](#modify-a-codex-record-39-s-metadata), when the Codex Record has been updated and logged on the blockchain.
codex-record:address-whitelisted       | [Codex Record](#codex-record) | Sent after someone [adds your application to a Codex Record's whitelisted addresses](#get-a-codex-record-39-s-whitelisted-addresses). This event is sent immediately as this is not an asynchronous action.
codex-record:transferred:new-owner     | [Codex Record](#codex-record) | Sent after [accepting an incoming transfer](#accept-a-transfer), when the Codex Record has been successfully transferred to your address and logged on the blockchain.
codex-record:transferred:old-owner     | [Codex Record](#codex-record) | Sent after someone [accepts one of your application's outgoing transfers](#accept-a-transfer), when the Codex Record has been successfully transferred to the recipient's address and logged on the blockchain.
codex-record:address-approved:owner    | [Codex Record](#codex-record) | Sent after [starting a transfer](#start-a-transfer), when the Codex Record's `approvedAddress` has been updated on the blockchain and is ready to be [accepted](#accept-a-transfer) by the recipient.
codex-record:address-approved:approved | [Codex Record](#codex-record) | Sent after someone [starts a transfer](#start-a-transfer) to your application, when the Codex Record's `approvedAddress` has been updated on the blockchain and is ready to be [accepted](#accept-a-transfer) by your application.

<!--
  @TODO: add these events to the table when CODX / fees are documented:

  codex-coin:transferred
  codex-coin:registry-contract-approved
-->

<aside class="notice">
  Not all webhook events are the result of an asynchronous action (e.g.
  <code>codex-record:address-whitelisted</code>), but all asynchronous actions
  have an associated webhook event.
</aside>

### Verifying Webhook Requests

> A simple webhook implementation that verifies webhook requests from The Codex API.

```javascript
import crypto from 'crypto'
import express from 'express'
import bodyParser from 'body-parser'

const app = express()

// your webhook will always be passed an application/json request
app.use(bodyParser.json())

app.post('/codex-webhook', (request, response) => {

  const { hash,  timestamp } = request.body

  // if, for some reason, your application's webhook is called without these
  //  parameters, the request most likely did not come from The Codex API and
  //  should be immediately rejected
  if (!hash || !timestamp) {
    response.sendStatus(400)
    return
  }

  // if the specified timestamp is in the future or is more than 5 minutes in
  //  the past, consider this a fraudulent request
  //
  // your "timestamp validity" window can be whatever you deem appropriate, but
  //  we recommend 5 minutes
  //
  // this is used to prevent the request from being "replayed" at a later date
  //  (via a man-in-the-middle or similar attack)
  const now = Date.now()

  if (timestamp > now || now - timestamp > 300000) {
    response.sendStatus(400)
    return
  }

  // construct the "valid hash" and compare with the hash sent by The  Codex API
  const validHash = crypto
    .createHmac('sha256', client_secret)
    .update(`${timestamp}:${client_id}`)
    .digest('hex')

  if (hash !== validHash) {
    response.sendStatus(401)
    return
  }

  // do whatever your application should do in response to this event here

  // let The Codex API know this webhook call was successful
  response.sendStatus(200)

})
```

> Remember to replace `client_id` and `client_secret` with your application's `client_id` and `client_secret`.

The `hash` and `timestamp` properties passed with every webhook request should
be used to verify that the request was actually sent by The Codex API and hasn't
been spoofed by an attacker. Your application should verify the hash for _all_
webhook events.

The verify the request, calculate the [sha256](https://nodejs.org/api/crypto.html#crypto_crypto_createhmac_algorithm_key_options)
hash of the string `${timestamp}:${client_id}` using your application's secret
as the `key`. Then compare the result with the `hash` specified in the request.


## Gas Allowance

Since each blockchain transaction The Codex API sends on behalf of your
application cost small amounts of Ether (known as "gas"), we limit all
applications to a certain amount of gas per period. This ensures The Codex API
is not abused.

At the start of each period, your gas allowance is reset. The following table
shows the reset periods for each environment:

Network | Period
------- | ----------------------------------------------------------------------
Ropsten | Every 24 hours
Rinkeby | Every 30 days
Mainnet | Every 30 days

<aside class="success">
  Currently, all applications receive <strong>40,500,000 gas</strong> per period.
  That's enough to create and transfer about 100 records. If you believe your
  application requires more gas per period, please
  <a href="mailto:help@codexprotocol.com?subject=Gas%20Allowance%20Increase%20Request&body=Let%20us%20know%20why%20your%20application%20needs%20a%20higher%20Gas%20Allowance.">contact us</a> and we can work
  something out.
</aside>


## Privacy

The public nature of blockchain presents some interesting challenges when
dealing with information many people people wish to keep private. To take
advantage of Ethereum's distributed, public ledger while still protecting
our user's private data, Codex Protocol has taken a hybrid "off-chain / on-chain"
approach to storing user data.

### "Off-Chain" vs "On-Chain" Data

All [metadata](#metadata) is stored "off-chain" (i.e. in a
[metadata provider](#metadata-provider)'s database), but _hashes_ of this data
are stored on "on-chain" (i.e in a Codex Protocol smart contract.) These hashes
can be used to verify the data stored in a metadata provider's database matches
the information on the blockchain, and therefore has not been tampered with.

Specifically, the only "on-chain" data are hashes of the Codex Record's name,
description, and associated files. Sending a request that will modify "on-chain"
data is considered an asynchronous action and will eventually result in your
application receiving a webhook event. See [webhooks](#webhooks) for details.

Updates to any other "off-chain" property of the Codex Record (such as the
`isPrivate` flag, or the `whitelistedAddresses` array), will be immediately
applied and (usually) won't cause a webhook event to be sent.

This "off-chain / on-chain" data separation is why there are two routes to
modify a Codex Record. When you [Modify a Codex Record](#modify-a-codex-record),
you're modifying it's "off-chain" data. When you
[Modify a Codex Record's Metadata](#modify-a-codex-record-39-s-metadata), you're
modifying it's "on-chain" data.

### Permissions

Some properties of a Codex Record and it's metadata are not returned in API
responses (i.e. they will be `undefined`) if the Codex Record marked as private
and the requesting user does not have the appropriate permissions.

There are three levels of permissions for Codex Records:

  1. **Owner** - full read & write permissions, all data always returned
  1. **Whitelisted Addresses** - read-only permissions, most data returned
  1. **Public Access / Non-Whitelisted Addresses** - read-only permissions, only public information returned

Additionally, there are two levels of privacy for a Codex Record:

  1. **`isPrivate`** - indicates that the Codex Record's metadata is private and can only be retrieved by the owner or whitelisted addresses.
  1. **`isHistoricalProvenancePrivate`** - indicates whether or not the Codex Record's [historical provenance](#historical-provenance) should be hidden, regardless of the value of `isPrivate`.

Individual permissions for each property of a Codex Record and it's metadata are
listed in [Response Examples](#response-examples).

<aside class="success">
  All Codex Records are fully private by default and must be explicitly
  <a href="#modify-a-codex-record">marked as public</a> by the owner.
</aside>

### Whitelisted Addresses

Every Codex Record has an "off-chain" list of Ethereum addresses allowed to view
it's private metadata. The `whitelistedAddresses` array allows users to provide
read-only access for any address they wish. See [Get a Codex Record's Whitelisted Addresses](#get-a-codex-record-39-s-whitelisted-addresses)
(and subsequent sections) for details on how to retrieve and update this list.

Even though private metadata is visible to whitelisted addresses, the
`isHistoricalProvenancePrivate` flag can be used to hide the
[historical provenance](#historical-provenance) for a Codex Record. This way,
the owner can keep some (presumably more confidential) files hidden while still
providing _some_ access to others.

### Approved Addresses

A Codex Record can also have an `approvedAddress`, which is an Ethereum address
that is allowed to transfer the Codex Record to itself. This is necessary for
the two-step transfer process. See [Transferring Codex Records](#transferring-codex-records)
for details.

<aside class="success">
  The <code>approvedAddress</code> is considered to be part of a Codex Record's
  <a href="#whitelisted-addresses">whitelisted addresses</a>, so addresses
  approved to transfer a Codex Record also have read-only permissions for
  private metadata.
</aside>


## Historical Provenance

In addition to an array of `images`, a Codex Record may also contain a special
array (`files`) that we call the "historical provenance". The intent is for
`images` to contain pictures and/or videos of the item itself and for `files`
to contain other, arbitrary files such as a PDF of physical appraisal documents
or a scan of an original purchase receipt.

Since files in historical provenance may be considered more "confidential", a
separate privacy control is available to keep these files hidden even when being
viewed by someone with [permissions to see private metadata](#permissions). If a
Codex Record's `isHistoricalProvenancePrivate` flag is `true`, the `files`
array will always be visible _to the owner only._


## Transferring Codex Records

Transferring a Codex Record is a two-step process:

  1. The owner [approves another user](#start-a-transfer) to accept the transfer
     of the Codex Record. This sets the Codex Record's `approvedAddress` to that
     user's Ethereum address, and grants them read-only access to the private
     metadata. At this point, the Codex Record is considered an
     [outgoing transfer](#get-outgoing-transfers) for the owner and an
     [incoming transfer](#get-incoming-transfers) for the approved user.

  1. The approved user must then explicitly [accept the incoming transfer](#accept-a-transfer).
     The approved user may also choose to [ignore the transfer](#ignore-a-transfer),
     in which case they remain the `approvedAddress` but the Codex Record can be
     hidden from their list of incoming transfers.

When the Codex Record is successfully transferred to the `approvedAddress` and
logged on the blockchain, the approved user becomes the new owner of the record
and a [Provenance Event](#provenance-event) is logged.

Additionally, a few "[off-chain](#quot-off-chain-quot-vs-quot-on-chain-quot-data)"
properties are reset to their default values:

Property                      | Reset To
----------------------------- | ------------------------------------------------
isPrivate                     | `true`
isIgnored                     | `false`
isInGallery                   | `false`
approvedAddress               | `null`
whitelistedAddresses          | `[]`
isHistoricalProvenancePrivate | `true`

<aside class="notice">
  If for some reason your application chooses to transfer a Codex Record to an
  Ethereum smart contract, that contract <em>must</em> implement the
  <code>ERC721TokenReceiver</code> interface as defined in the
  <a href="https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md" rel="noopener noreferrer">specification</a>.
  Otherwise, the transfer will fail because the contract cannot accept ERC-721
  tokens.
</aside>


<!--
@TODO: add these sections when CODX / fees are documented
## CODX Tokens & Fees
## ERC-721 Tokens
-->

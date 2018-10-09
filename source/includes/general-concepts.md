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
[Canceling a Transfer](#cancel-a-transfer)                                  | `codex-record:address-approved:cancel`
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
codex-record:address-approved:cancel   | [Codex Record](#codex-record) | Sent after [canceling a transfer](#cancel-a-transfer), the Codex Record's `approvedAddress` has been updated and logged on the blockchain.
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

**@TODO:** talk about gas allowances and when they reset, also list some gas metrics


## Zero Address

**@TODO:** talk about how the zero address will be used in provenance events


## Privacy

**@TODO:** talk about how Codex Records are private by default

### Permissions

### Approved Addresses

### Whitelisted Addresses


## Historical Provenance

**@TODO:** talk about the `files` array in a Codex Record's metadata


## "Off-Chain" vs "On-Chain" Data

**@TODO:** talk about a Codex Record vs it's metadata during creation & modification


## Transferring Codex Records

**@TODO:** talk about the two-step transfer process, what gets reset on transfer


<!--
@TODO: add these sections when CODX / fees are documented
## CODX Tokens & Fees
## ERC-721 Tokens
-->

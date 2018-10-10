# Public Routes

Unlike [client routes](#client-routes), public routes do not require an
`accessToken` in the Authorization header. However, sometimes an `accessToken`
is required to retrieve a full response.

For example, to retrieve public information about a Codex Record, you could call
`GET /v1/record/:tokenId` without an `accessToken`. If the Codex Record is
public, then you will receive a full response. However, if the Codex Record is
private, calling this route without an `accessToken` (or an `accessToken` that
doesn't belong to the owner of the Codex Record) will return a response with all
private information removed (i.e. no `metadata`).

Similarly, you will not be able to call public routes such as `GET /v1/record/:tokenId/metadata`
if the record is private and the `accessToken` is missing (or if you provide
your `accessToken` but you are not the owner.)

<aside class="success">
  It's generally a good idea to just <em>always</em> include an
  <a href="#access-tokens">Access Token</a> with every API request.
</aside>

## Get an Access Token

This endpoint can be used to generate an access token, which is required for
[making authenticated requests](#making-authenticated-requests).

<aside class="warning">
  When requesting access tokens, you <em>must</em> send the request as
  <code>Content-Type: application/x-www-form-urlencoded</code>. This is required
  by the <a href="https://tools.ietf.org/html/rfc6749?#section-4.1.3" rel="noopener noreferrer">OAuth2 Specification</a>.
</aside>

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/oauth2/token',
  method: 'post',
  json: true,

  // by using "form" here instead of "body", request will send this request as
  //  application/x-www-form-urlencoded
  form: {
    grant_type: 'client_credentials',
    client_id: '5bae56e05d7971becf854a70',
    client_secret: '352db118-b76c-4c60-9ed8-aa13cb4621b8',
  },
}

request(options, (error, response) => {
  console.log(response.body.result.accessToken) // also available is
})
```

> The above API call returns an [OAuth2 Access Token](#oauth2-access-token).

> Remember to replace the example `client_id` and `client_secret` with your application's `client_id` and `client_secret`.

### HTTP Request

`GET /v1/oauth2/token`

### Request Parameters

Parameter     | Type   | Description
------------- | ------ | -------------------------------------------------------
client_id     | String | The unique identifier for your application, provided by Codex.
grant_type    | String | Must be `client_credentials`.
client_secret | String | Your application's secret, provided by Codex.

<aside class="notice">
  This route has <code>snake_case</code> parameters, as required by the
  <a href="https://tools.ietf.org/html/rfc6749?#section-4.1.3" rel="noopener noreferrer">OAuth2 Specification</a>.
  This is the only route with <code>snake_case</code> parameters, all other
  routes have <code>camelCase</code> parameters.
</aside>


## Get a Codex Record

This endpoint can be used to retrieve a specific Codex Record. This will return
a full [Codex Record](#codex-record) document, including it's
[metadata](#metadata) and [provenance](#provenance-event).

<aside class="warning">
  If the Codex Record is private, certain fields (e.g. <code>metadata</code>)
  will be <code>undefined</code>. Additionally, some fields may be
  <code>undefined</code> if you are not the owner of the Codex Record. See the
  <a href="#codex-record">Codex Record</a> description for details.
</aside>

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/records/0',
  method: 'get',
  json: true,
}

request(options, (error, response) => {
  console.log(response.body.result) // Codex Record with tokenId 0
})
```

> The above API call returns a single [Codex Record](#codex-record).

### HTTP Request

`GET /v1/records/:tokenId`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record to retrieve.


## Get a Codex Record's Metadata Only

This endpoint can be used to retrieve _only_ the [metadata](#metadata) of a
specific Codex Record. This is useful if you do not need the entire Codex Record
document.

<aside class="warning">
  This route will return <code>null</code> if the Codex Record is private,
  unless you are the owner or a whitelisted address. See
  <a href="#privacy">Privacy</a> for details.
</aside>

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/records/0/metadata',
  method: 'get',
  json: true,
}

request(options, (error, response) => {
  console.log(response.body.result) // the metadata of Codex Record with tokenId 0
})
```

> The above API call returns a Codex Record's [metadata](#metadata).

### HTTP Request

`GET /v1/records/:tokenId/metadata`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to retrieve the metadata of.


## Get a Codex Record's Provenance Only

This endpoint can be used to retrieve _only_ the [provenance](#provenance-event)
of a specific Codex Record. This is useful if you do not need the entire Codex
Record document.

<aside class="notice">
  This route always returns an array, because "provenance" is really a list of
  events.
</aside>


```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/records/0/provenance',
  method: 'get',
  json: true,

  // sort by date created in reverse
  body: {
    order: '-createdAt',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // the provenance of Codex Record with tokenId 0
})
```

> The above API call returns a Codex Record's [provenance](#provenance-event).

### HTTP Request

`GET /v1/records/:tokenId/provenance`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to retrieve the provenance of.

### Request Parameters

Parameter    | Type   | Default   | Description
------------ | ------ | --------- | --------------------------------------------------------
order        | String | createdAt | To sort in reverse order, specify this value as `-createdAt`.


## Get a Codex Record's Main Image Only

This endpoint can be used to retrieve _only_ the main image for a specific Codex
Record. This is useful if you do not need the entire Codex Record document.

<aside class="warning">
  This route will return <code>null</code> if the Codex Record is private,
  unless you are the owner or a whitelisted address. See
  <a href="#privacy">Privacy</a> for details.
</aside>


```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/records/0/main-image',
  method: 'get',
  json: true,

  // sort by date created in reverse
  body: {
    order: '-createdAt',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // the mainImage of Codex Record with tokenId 0
})
```

> The above API call returns a single [File](#file).

### HTTP Request

`GET /v1/records/:tokenId/main-image`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to retrieve the main image of.


## Get a Codex Record's Images Only

This endpoint can be used to retrieve _only_ the `images` array for a specific
Codex Record. This is useful if you do not need the entire Codex Record document.

<aside class="warning">
  This route will return <code>null</code> if the Codex Record is private,
  unless you are the owner or a whitelisted address. See
  <a href="#privacy">Privacy</a> for details.
</aside>


```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/records/0/images',
  method: 'get',
  json: true,

  // sort by date created in reverse
  body: {
    order: '-createdAt',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // the images array of Codex Record with tokenId 0
})
```

> The above API call returns an array of [Files](#file).

### HTTP Request

`GET /v1/records/:tokenId/images`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to retrieve the images array of.


## Get a Codex Record's Files Only

This endpoint can be used to retrieve _only_ the `files` array for a specific
Codex Record. This is useful if you do not need the entire Codex Record document.

<aside class="warning">
  This route will return <code>null</code> if the Codex Record is private,
  unless you are the owner or a whitelisted address. See
  <a href="#privacy">Privacy</a> for details.
</aside>


```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/records/0/files',
  method: 'get',
  json: true,

  // sort by date created in reverse
  body: {
    order: '-createdAt',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // the files array of Codex Record with tokenId 0
})
```

> The above API call returns an array of [Files](#file).

### HTTP Request

`GET /v1/records/:tokenId/files`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to retrieve the files array of.

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

## Get a Specific Codex Record

This endpoint can be used to retrieve a specific Codex Record.

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/records/0',
  method: 'get',
  json: true,
}

request(options, (error, response) => {
  console.log(response.body.result) // Codex Record with tokenId 0
})
```

> The above API call returns a single <a href="#codex-record">Codex Record</a>.

### HTTP Request

`GET /v1/records/:tokenId`

### URL Parameters

Parameter    | Description
------------ | -----------
tokenId      | The `tokenId` of the Codex Record to retrieve.

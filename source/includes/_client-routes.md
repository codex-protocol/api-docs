# Client Routes

Unlike [public routes](#public-routes), client routes are all prefixed with
`/v1/client` and only perform actions on data owned by your application. All
client routes require an `accessToken` in the Authorization header, as described
in the <a href="#access-tokens">Access Tokens</a> section.

<aside class="success">
  All requests to client routes require an <a href="#access-tokens">Access Token</a>.
</aside>


## Get Client

This endpoint can be used to retrieve the details of your application. This is
useful if you want to check your [gas allowance](#gas-allowance) before sending
a request that would consume gas.

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client',
  method: 'get',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // your Client record
})
```

> The above API call returns a single <a href="#client">Client</a>.


### HTTP Request

`GET /v1/client`


## Get All Codex Records

This endpoint can be used to retrieve all Codex Records _that your application
currently owns._

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client/records',
  method: 'get',
  json: true,

  // sort by date created in reverse and get 5 records, skipping the first ten
  body: {
    limit: 5,
    offset: 10,
    order: '-createdAt',
  }

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // an array of your application's Codex Records
})
```

> The above API call returns an array of <a href="#codex-record">Codex Records</a>.

### HTTP Request

`GET /v1/client/records`

### Query Parameters

Parameter    | Type   | Default   | Description
------------ | ------ | --------- | --------------------------------------------
limit        | Number | 100       | How many records to retrieve; use with `offset` to paginate through Codex Records.
offset       | Number | 0         | How many records to skip before applying the `limit`; use with limit to paginate through Codex Records.
order        | String | createdAt | To sort in reverse order, specify this value as `-createdAt`.

<aside class="notice">
  There is no public route to list all Codex Records; you may only list Codex
  Records that your application currently owns.
</aside>

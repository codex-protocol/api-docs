# Authentication

## Registering an Application

Currently there is no admin interface built out for application developers, so
the process for registering an application requires manual steps from both
Codex and the application developer. To register an application, [send us an email](mailto:developers@codexprotocol.com?subject=Codex%20API%20Application%20Request&body=Please%20provide%20a%20brief%20description%20of%20your%20application.) with the following information:

Property   | Description
---------- | -------------------------------------------------------------------
name       | The name of the application. This will be shown in The Codex Viewer as a registered application, taking the place of your application's Ethereum address in the Codex Record’s provenance.
email      | The application developer’s email address. This will be used to communicate any breaking API changes or development updates.
webhookUrl | For example, `https://your-api.example.com/codex-webhook`. Since blockchain transactions are asynchronous, this `webhookUrl` will be used to inform your application that an event has occurred, for example when your Codex Records have been created. See [webhooks](#webhooks) for details.

<aside class="warning">
  We only support the <code>client_credentials</code> OAuth2 grant, which means
  that calls to The Codex API <strong>must</strong> be from a "confidential
  client." A confidential client is an application that can protect secrets from
  public users - that is to say <strong>all calls to The Codex API should
  originate from a server application and not from within a browser.</strong>
</aside>


## Access Tokens

### Obtaining an Access Token

Before you can make API calls, you'll need an access token. See
[Get an Access Token](#get-an-access-token) for more details.

### Access Token Expiration

> Example response when an expired access token is provided:

```javascript
{
  "error": {
    "code": 401,
    "message": "Invalid token: access token is invalid"
  },
  "result": null
}
```

For security purposes, access tokens will expire after a certain period of time.
If you send a request and and receive a [401 Unauthorized](#errors) error,
you'll need to [generate another access token](#get-an-access-token) and send
the request again.

The following table shows the access token expiries for each environment:

Network | Expiry
------- | ----------------------------------------------------------------------
Ropsten | 30 days after creation
Rinkeby | 90 days after creation
Mainnet | 90 days after creation

<aside class="success">
  It's a good idea to simply request a new access token periodically, before
  your current access token is set to expire. Be sure to destroy your existing
  access token after requesting a new one to minimize the potential for
  malicious use.
</aside>


## Making Authenticated Requests

> Making an authenticated API call:

```javascript
import request from 'request'

// retrieve a list of your application's Codex Records
const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/client/records',
  method: 'get',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // an array of Codex Records
})
```

> Remember to replace the example `accessToken` with your personal `accessToken`.

The Codex API expects an OAuth2 access token to be included with all requests
made to the server in the Authorization header as follows:

`Authorization: Bearer d49694e5a3459759cc7ac1741de246e184e51d6e`

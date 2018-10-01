# Authentication

## Registering an Application
Currently there is no admin interface built out for application developers, so
the process for registering an application requires a manual steps from both
Codex and the application developer. Additionally, we only support the
`client_credentials` grant, which means that calls to The Codex API
<strong>must</strong> be confidential client. A confidential client is an
application that can protect secrets from public users (i.e. calls to The Codex
API should be happening on a server application and not from within the browser.)

To register an application, send us email with the following information:


  - `name`: The name of the application. This will be shown in the Codex Viewer
    as a registered application, taking the place of the Ethereum address in the
    Codex Record’s provenance section.

  - `email`: The application developer’s email address. This will be used to
    communicate any breaking API changes or development updates.

  - `webhookUrl`: e.g. `https://your-api.example.com/codex-webhook`. Since
    blockchain transactions are asynchronous, this webhookUrl will be used to
    inform your application that an event has occurred, for example when your
    Codex Records have been minted.

## Access Tokens

> Obtaining an access token:

```javascript
import request from 'request'

const options = {
  url: 'http://localhost:3001/v1/oauth2/token',
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
  console.log(response.body.result.accessToken)
})
```

> Remember to replace the example client_id and client_secret with your applications client_id and client_secret.

Before you can make API calls, you'll need an access token. Send a POST request
to `/v1/oauth2/token` with your `client_id` and `client_secret` (provided to
you by Codex.) You must also specify a `grant_type`, but the only grant type
supported is `client_credentials`.

<!--
  @TODO: is this "warning" class too much? maybe it should just be a "notice",
  but it feels important to call this out
-->
<aside class="warning">
  When requesting access tokens, you <em>must</em> send the request as
  <code>Content-Type: application/x-www-form-urlencoded</code>. This is required
  by the <a href="https://tools.ietf.org/html/rfc6749?#section-4.1.3" target="_blank">OAuth2 Specification</a>.
</aside>

## Making API Calls
> Making an authenticated API call:

```javascript
import request from 'request'

// retrieve a list of your application's Codex Records
const options = {
  url: 'http://rinkeby-codex-registry-api.codexprotocol.com/v1/client/records',
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

> Remember to replace the example access token with your personal access token.

The Codex API expects an OAuth2 access token to be included with all requests
made to the server in the Authorization header as follows:

`Authorization: Bearer d49694e5a3459759cc7ac1741de246e184e51d6e`

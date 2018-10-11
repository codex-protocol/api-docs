# Introduction

Welcome to The Codex API documentation! Among other things, you can use our API
to create Codex Records on the Ethereum Blockchain without the need for any
direct blockchain interaction.

To use The Codex API, you will need to register an OAuth2 application with us.
See [Authentication](#authentication) for more information.

If you have any questions or suggestions for edits to this documentation, please
visit the [GitHub](https://github.com/codex-protocol/site.dev-codexprotocol-com)
repository and open an [issue](https://github.com/codex-protocol/site.dev-codexprotocol-com/issues)
and/or [pull request.](https://github.com/codex-protocol/site.dev-codexprotocol-com/pulls)

<aside class="notice">
  For the sake of brevity, all JavaScript code examples in this documentation
  assume the use of a convenient HTTP request library (e.g.
  <a href="https://www.npmjs.com/package/request" rel="noopener noreferrer">request</a>).
  Of course, you're free to make requests to The Codex API in any manner you see
  fit.
</aside>

## API Endpoints

The API endpoints vary based on which environment you are building against. The Codex API is available in 3 environmnets that correspond to 3 different Ethereum networks.

### Ropsten Testnet (development)

The Ropsten Testnet is our development environment. We frequently deploy here and it may be broken at times. It is not recommended to build or test your application in this environment unless you are looking to get the absolute latest changes on the protocol.

`http://ropsten-api.codexprotocol.com`

<aside class="warning">
  Note the HTTP instead of HTTPS for Ropsten
</aside>

### Rinkeby (staging)

We use the Rinkeby Testnet as our staging environment. We only deploy stable builds here, prior to production. This is the recommended place to test your application before launching in production.

`https://rinkeby-api.codexprotocol.com`

### Mainnet (production)

The Mainnet environment corresponds to the production environment for The Codex API. Changes only get pushed here after being validated in our development/staging environments. All Codex Records created here are immutably recorded on the blockchain and cannot be destroyed. Integration into the mainnet environment should only be done after ensuring your application runs smooth in staging.

`https://api.codexprotocol.com`

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


## API URLs

The Codex API is available in 3 environments that correspond to 3 different
Ethereum networks, so the API URL will vary based on the environment you are
developing against.

Environment | Network | URL | Description
----------- | ------- | --- | --------------------------------------------------
Development | Ropsten | [http://ropsten-api.codexprotocol.com](http://ropsten-api.codexprotocol.com) | We frequently deploy (possibly breaking) changes here. It is not recommended to develop your application against this network unless you are looking to test the latest changes to the Codex Protocol.
Staging     | Rinkeby | [https://rinkeby-api.codexprotocol.com](https://rinkeby-api.codexprotocol.com) | We only deploy stable builds here, prior to Mainnet. This is the recommended network to test your application before deploying to production.
Production  | Mainnet | [https://api.codexprotocol.com](https://api.codexprotocol.com) | Changes only get deployed here after being validated in our development and staging environments. All Codex Records created here are immutably recorded on the blockchain and cannot be destroyed. Integration into Mainnet should only be done after ensuring your application runs smoothly in the staging environment.

<aside class="success">
  All code examples in this documentation use the Rinkeby API URL, since that is
  the recommended network to develop against.
</aside>

<aside class="warning">
  Note the <code>http</code> instead of <code>https</code> for Ropsten.
</aside>

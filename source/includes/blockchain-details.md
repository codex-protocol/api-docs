# Blockchain Details

This section contains details about the blockchain specifics of Codex Protocol.
It's geared towards developers who are building blockchain-based applications
that interface directly with our smart contracts, or who are building NFT
wallets and want to correctly display Codex Records.


## Smart Contract Addresses

The following table lists the contracts for each environment we deploy to. Click
a link to check out that contract on Etherscan.

Ropsten                   | Rinkeby                   | Mainnet
------------------------- | ------------------------- | ------------------------
[CodexCoin](https://ropsten.etherscan.io/token/0xc50efb6572218614d743c9929bd93b0a1a3982a9) | [CodexCoin](https://rinkeby.etherscan.io/address/0xe33345607c3065578f11ea456834ff9e82739d56) | [CodexCoin](https://etherscan.io/token/0xf226e38c3007b3d974fc79bcf5a77750035436ee)
[CodexRecord](https://ropsten.etherscan.io/address/0xd8d4c451472727309b123850cbe2f9852163e8a4) | [CodexRecord](https://rinkeby.etherscan.io/address/0xcd3dd2dabc7c658c1abcfe8e8a63574447985b5d) | [CodexRecord](https://etherscan.io/address/0x2c00a92a9c5c919a1a4b5a8ee6bc520f61dbe421)
[CodexRecordProxy](https://ropsten.etherscan.io/address/0x714cd5d6425ef198768d504edf190b5aa5b44334) | [CodexRecordProxy](https://rinkeby.etherscan.io/address/0xa3fb132c4622db86bbf39cf5e6301d8a2a1145a8) | [CodexRecordProxy](https://etherscan.io/address/0x8853b05833029e3cf8d3cbb592f9784fa43d2a79)
[CodexStakeContract](https://ropsten.etherscan.io/address/0x0bb7d24b10768431b5b7bda9afde822ca2ff3ad6) | [CodexStakeContract](https://rinkeby.etherscan.io/address/0x8fa7c01220396a205181d289ea805023abecce61) | [CodexStakeContract](https://etherscan.io/address/0xdea454c9c4ad408f324cc0ea382b2b7aad99640c)
[IdentityPlatform](https://ropsten.etherscan.io/address/0x04e8c935b3b889de1a4bba16a19803eda51ef9d5) | [IdentityPlatform](https://rinkeby.etherscan.io/address/0x72e5ee7b7c16b321e74f2d64be18fa5b4ab648a2) | [IdentityPlatform](https://ropsten.etherscan.io/address/0x85e7453e884e26f9fb19f2e6487446740658e5ce)

<aside class="warning">
  The Rinkeby &amp; Ropsten addresses are subject to change, but the Mainnet
  addresses will never change.
</aside>


## Get ERC-721 Token Metadata

> Get a token's metadata for display in an Ethereum wallet with NFT support:

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/token-metadata/0',
  method: 'get',
  json: true,
}

request(options, (error, response) => {
  console.log(response.body) // ERC-721 Metadata JSON for Codex Record with tokenId 0
})
```

> The above API call returns [ERC-721 Metadata JSON](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md), e.g.

```json
{
  "name": "Really Cool Codex Record",
  "description": "This is a really cool Codex Record!",
  "image": "https://s3.amazonaws.com/codex.registry/files/1538152180696.c8a9ec69-0ed8-4516-a34c-e15ebbe4749c.jpg"
}
```

Our smart contract implements the optional ERC-721 metadata extension, which
defines a unique tokenURI for each token in the registry. Since token metadata
is dynamic, the tokenURIs point to an endpoint in our API instead of a static
file on IPFS.

<aside class="warning">
  This endpoint is specifically for developers building Ethereum wallets with
  NFT support and <strong>should not be used by OAuth2 clients</strong>.
</aside>

### HTTP Request

`GET /token-metadata/:tokenId`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to retrieve the metadata of.

<aside class="success">
  This endpoint does not wrap the response in the normal
  <code>{ error, response }</code> object, as described in
  <a href="#response-examples">Response Examples</a>. Instead, this endpoint
  returns <em>ERC-721 Metadata JSON</em> as described in the
  <a href="https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md" rel="noopener noreferrer">ERC-721 specification</a>.
</aside>

<aside class="notice">
  If the Codex Record is private, then generic placeholder information will be
  returned instead of actual metadata.
</aside>

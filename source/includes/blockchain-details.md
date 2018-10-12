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
[CodexCoin](https://ropsten.etherscan.io/token/0x2af5409438d2e6c241015f3130213f6a122b4064) | [CodexCoin](https://rinkeby.etherscan.io/address/0x6a8c5db1495ffc4ef183dfccfdc4de5164b4e95c) | [CodexCoin](https://etherscan.io/token/0xf226e38c3007b3d974fc79bcf5a77750035436ee)
[CodexRecord](https://ropsten.etherscan.io/address/0xd8d4c451472727309b123850cbe2f9852163e8a4) | [CodexRecord](https://rinkeby.etherscan.io/address/0xcd3dd2dabc7c658c1abcfe8e8a63574447985b5d) | [CodexRecord](https://etherscan.io/address/0x2c00a92a9c5c919a1a4b5a8ee6bc520f61dbe421)
[CodexRecordProxy](https://ropsten.etherscan.io/address/0x714cd5d6425ef198768d504edf190b5aa5b44334) | [CodexRecordProxy](https://rinkeby.etherscan.io/address/0xa3fb132c4622db86bbf39cf5e6301d8a2a1145a8) | [CodexRecordProxy](https://etherscan.io/address/0x8853b05833029e3cf8d3cbb592f9784fa43d2a79)
[CodexStakeContract](https://ropsten.etherscan.io/address/0x0bb7d24b10768431b5b7bda9afde822ca2ff3ad6) | [CodexStakeContract](https://rinkeby.etherscan.io/address/0x2bbcae1335a97e440c7d9f3f638db26abfad3207) | [CodexStakeContract](https://etherscan.io/address/0xdea454c9c4ad408f324cc0ea382b2b7aad99640c)
[IdentityPlatform](https://ropsten.etherscan.io/address/0xbb295f65335caef0cbe0e31a24466a01004a8067) | [IdentityPlatform](https://rinkeby.etherscan.io/address/0x7293bae565cb2d78eb8e0600e6da7397823edfdc) | N/A

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

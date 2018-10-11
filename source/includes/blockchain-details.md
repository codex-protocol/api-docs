# Blockchain details

The below section goes into details about the blockchain specifics of Codex Protocol. It's geared towards developers that are building blockchain based applications that are looking to interface directly with the smart contracts or building NFT wallets that want to be able to correctly display Codex Records.

## Smart contract addresses

The Rinkeby/Ropsten addresses are subject to change, but the Mainnet addresses will never change.

Ropsten              | Rinkeby              | Mainnet
---------------------| ---------------------| ---------------------
[CodexCoin](https://ropsten.etherscan.io/token/0x2af5409438d2e6c241015f3130213f6a122b4064)                               | [CodexCoin](https://rinkeby.etherscan.io/address/0x6a8c5db1495ffc4ef183dfccfdc4de5164b4e95c)        | [CodexCoin](https://etherscan.io/token/0xf226e38c3007b3d974fc79bcf5a77750035436ee)
[CodexRecord](https://ropsten.etherscan.io/address/0xd8d4c451472727309b123850cbe2f9852163e8a4)          | [CodexRecord](https://rinkeby.etherscan.io/address/0xcd3dd2dabc7c658c1abcfe8e8a63574447985b5d)        | [CodexRecord](https://etherscan.io/address/0x2c00a92a9c5c919a1a4b5a8ee6bc520f61dbe421)
[CodexRecordProxy](https://ropsten.etherscan.io/address/0x714cd5d6425ef198768d504edf190b5aa5b44334)     | [CodexRecordProxy](https://rinkeby.etherscan.io/address/0xa3fb132c4622db86bbf39cf5e6301d8a2a1145a8)        | [CodexRecordProxy](https://etherscan.io/address/0x8853b05833029e3cf8d3cbb592f9784fa43d2a79)
[CodexStakeContract](https://ropsten.etherscan.io/address/0x0bb7d24b10768431b5b7bda9afde822ca2ff3ad6)   | [CodexStakeContract](https://rinkeby.etherscan.io/address/0x2bbcae1335a97e440c7d9f3f638db26abfad3207)        | [CodexStakeContract](https://etherscan.io/address/0xdea454c9c4ad408f324cc0ea382b2b7aad99640c)
[IdentityPlatform](https://ropsten.etherscan.io/address/0xbb295f65335caef0cbe0e31a24466a01004a8067)     | [IdentityPlatform](https://rinkeby.etherscan.io/address/0x7293bae565cb2d78eb8e0600e6da7397823edfdc)        | N/A

## ERC-721 Token Metadata

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/token-metadata/0',
  method: 'get',
  json: true,
}

request(options, (error, response) => {
   // Codex Record with tokenId 0
  console.log(response.body)
})

// Example response
{
  {
    "name": "placeholder name",
    "description": "placeholder description",
    "image": "imageURI"
  }
}
```

Our smart contract implements the optional ERC-721 metadata extension which defines a unique tokenURI for each token in the registry. Since token metadata is dynamic, the tokenURIs point to an endpoint in our API instead of a static file on IPFS.

`GET /token-metadata/:tokenId`

<aside class="warning">
 The token metadata endpoint is a special endpoint in that it doesn't wrap the resulting JSON in a <code>response</code> object. The raw JSON returned is the result as defined in the ERC-721 specification.
</aside>

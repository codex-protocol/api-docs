# General Concepts

This section explains miscellaneous concepts referenced throughout this
documentation.


## API Requests & Responses

**@TODO:** talk about the various ways you can make API calls, also response
structure


## Webhooks

**@TODO:** talk about asynchronous blockchain transactions, webhook request
structure, and describe all events

### Webhook Events

Event Name                             | Payload                       | Description
-------------------------------------- | ----------------------------- | -----------
codex-record:minted                    | [Codex Record](#codex-record) |
codex-record:modified                  | [Codex Record](#codex-record) |
codex-record:address-whitelisted       | [Codex Record](#codex-record) |
codex-record:transferred:new-owner     | [Codex Record](#codex-record) |
codex-record:transferred:old-owner     | [Codex Record](#codex-record) |
codex-record:address-approved:owner    | [Codex Record](#codex-record) |
codex-record:address-approved:cancel   | [Codex Record](#codex-record) |
codex-record:address-approved:approved | [Codex Record](#codex-record) |
codex-coin:transferred                 | Number                        |
codex-coin:registry-contract-approved  | Number                        |


## Gas Allowance

**@TODO:** talk about gas allowances and when they reset, also list some gas metrics


## CODX Tokens & Fees

**@TODO:** talk about CODX and fees


## Zero Address

**@TODO:** talk about how the zero address will be used in provenance events


## Privacy

**@TODO:** talk about how Codex Records are private by default

### Permissions

### Approved Addresses

### Whitelisted Addresses


## Historical Provenance

**@TODO:** talk about the `files` array in a Codex Record's metadata


## "Off-Chain" vs "On-Chain" Data

**@TODO:** talk about a Codex Record vs it's metadata during creation & modification


## Transferring Codex Records

**@TODO:** talk about the two-step transfer process, what gets reset on transfer

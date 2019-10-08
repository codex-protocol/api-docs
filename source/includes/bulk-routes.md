# Bulk Routes

Bulk routes are a subset of [client routes](#client-routes), which means they
are also prefixed with `/v1/client` and only perform actions on data owned by
your application. All bulk routes require an `accessToken` in the Authorization
header, as described in the <a href="#access-tokens">Access Tokens</a> section.

However, bulk routes are listed separately here because they work a bit
differently. They are designed to take large sets of data and process actions in
a queue. For example, these bulk routes can be used to [create hundreds of Codex Records](#create-codex-records-in-bulk)
with just one API request.

<aside class="success">
  All requests to bulk routes require an <a href="#access-tokens">Access Token</a>.
</aside>


## Create Codex Records in Bulk

This endpoint can be used to create a large number of Codex Records with just
one API request. This route is essentially the same as
[the single-record creation route](#create-a-codex-record), but it takes an
array of metadata objects instead of a single metadata object. Another
difference is that this route takes URLs for files and NOT file data (these
files are downloaded an re-hosted by Codex during processing.)

<aside class="success">
  This is an asynchronous action. When all Codex Records have been created and
  logged on the blockchain, your application's webhook will be called with the
  Bulk Transaction in the request body. See <a href="#webhooks">webhooks</a>
  for details.
</aside>

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/client/bulk-transaction/record-mint',
  method: 'post',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  body: {
    proxyUserAddress: '0xf17f52151ebef6c7334fad080c5704d77216b732'
    metadata: [
      {
        isPrivate: true,
        isHistoricalProvenancePrivate: true,
        name: 'Really Cool Codex Record #1',
        description: 'This is a really cool Codex Record!',

        mainImageUrl: 'https://example.com/images/cool-main-image.jpg',
        imageUrls: [
          'https://example.com/images/cool-supplemental-image-1.jpg',
          'https://example.com/images/cool-supplemental-image-2.jpg',
        ],
        fileUrls: [
          'https://example.com/files/author-notes.txt',
          'https://example.com/files/certificate-of-authenticity.pdf',
        ],

        additionalMetadata: {
          lotNumber: '123A',
          salePrice: '1000',
          saleCurrency: 'USD',
          editionNumber: '16'
          totalEditions: '64'
          condition: 'Excellent',
          dimensionsInches: '18 x 23',
          creatorName: 'Cool Guy McGee',
          auctionName: 'My Cool Auction',
          medium: 'mixed media on paper',
          signatureLocation: 'front, top left',
          dimensionsCentimeters: '45.7 x 58.4',
          assetSoldAt: '2019-03-21T16:30:06.030Z',
          assetCreatedAt: '1993-01-01T00:00:00.000Z',
        },

        auctionHouseMetadata: {
          id: '1234',
          linkbackUrl: 'https://example.com/my-auction-house/asset/1234',

          // any other arbitrary fields
          transactionId: 27299,
          inventoryNumber: 'TWC1123-G16926-001',
        },
      },
      {
        isPrivate: false,
        isHistoricalProvenancePrivate: false,
        name: 'Really Cool Codex Record #2',
        description: 'This is also a really cool Codex Record!',

        mainImageUrl: 'https://example.com/images/another-cool-main-image.jpg',
        imageUrls: [
          'https://example.com/images/another-cool-supplemental-image-1.jpg',
          'https://example.com/images/another-cool-supplemental-image-2.jpg',
        ],
        fileUrls: [
          'https://example.com/files/more-author-notes.txt',
          'https://example.com/files/another-certificate-of-authenticity.pdf',
        ],

        additionalMetadata: {
          lotNumber: '123A',
          condition: 'Poor',
          editionNumber: '16'
          totalEditions: '64'
          salePrice: '2000',
          saleCurrency: 'USD',
          medium: 'linocut in 4 colors',
          creatorName: 'Cool Guy McGee',
          auctionName: 'My Cool Auction',
          dimensionsInches: '7 1/2 x 11 3/4',
          dimensionsCentimeters: '19.1 x 29.8',
          signatureLocation: 'back, bottom left',
          assetSoldAt: '2019-03-20T14:30:06.030Z',
          assetCreatedAt: '1976-01-01T00:00:00.000Z',
        },

        auctionHouseMetadata: {
          id: '4321',
          linkbackUrl: 'https://example.com/my-auction-house/asset/4321',

          // any other arbitrary fields
          transactionId: 27300,
          inventoryNumber: 'TWC1123-G16926-002',
        },
      },

      // more metadata objects here

    ],
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // a Bulk Transaction object
})
```

> The above API call (eventually) creates two Codex Records and returns a single [Bulk Transaction](#bulk-transaction).

### HTTP Request

`POST /v1/client/bulk-transaction/record-mint`

### Webhook Event

Event Name                   | Recipient
---------------------------- | -------------------------------------------------
`bulk-transaction:started`   | Bulk Transaction Creator (and _not_ the proxy user, if specified)
`bulk-transaction:completed` | Bulk Transaction Creator (and _not_ the proxy user, if specified)

### Request Parameters

Parameter                                 | Type    | Description
----------------------------------------- | ------- | -----------------------------------
proxyUserAddress                          | String  | _(Optional)_ The Ethereum address of the user to create these Codex Records for. This field is only for applications with [Proxy Users](#proxy-users).
metadata[][name]                          | String  | The plain text name of this Codex Record.
metadata[][isPrivate]                     | Boolean | _(Optional, default `false`)_ This flag indicates that the metadata for the Codex Record is private and can only be retrieved by the owner, the `approvedAddress`, and the addresses listed in `whitelistedAddresses` / `whitelistedEmails`.
metadata[][isHistoricalProvenancePrivate] | Boolean | _(Optional, default `true`)_ This flag indicates whether or not [historical provenance](#historical-provenance) (i.e. `metadata[][files]`) should be hidden, regardless of the value of `isPrivate`.
metadata[][description]                   | String  | _(Optional)_ The plain text description of this Codex Record. The field can be set to `null` or simply omitted to leave the description empty.
metadata[][mainImageUrl]                  | String  | The main image of this Codex Record.
metadata[][imageUrls]                     | String  | _(Optional)_ An array of supplemental images that belong to this metadata.
metadata[][fileUrls]                      | String  | _(Optional)_ An array of supplemental files that belong to this metadata. This is considered the "[historical provenance](#historical-provenance)" of the Codex Record.
metadata[][additionalMetadata]            | Object  | _(Optional)_ A list of additional metadata about this asset. See [Additional Metadata](#additional-metadata) for details.
metadata[][auctionHouseMetadata]          | Object  | An arbitrary list of data specific to your application (this field is ignored if your application is not an auction house.) IF your account is an auction house account, this field is required (as well as the `auctionHouseMetadata.id` parameter.) See [Auction House Metadata](#auction-house-metadata) for details.

<aside class="success">
  All files (<code>mainImageUrl</code>, <code>imageUrls</code>, and
  <code>fileUrls</code>) are downloaded and re-hosted by Codex. These URLs do
  not need to be permanent, but they should be available until the entire bulk
  transaction has finished processing. If a file URL does not return a 2XX
  response, the Codex Record will not be created.
</aside>

<aside class="warning">
  This route is written to be as forgiving as possible, so if some metadata
  objects contain errors, as many valid records as possible will be created and
  any errors will be returned in the Bulk Transaction object. See
  <a href="#bulk-transaction">Bulk Transaction</a> for details.
</aside>

<aside class="notice">
  This route returns a <a href="#bulk-transaction">Bulk Transaction</a> object,
  which can be used in conjunction with the <a href="#get-a-bulk-transaction">
  Get a Bulk Transaction</a> route to monitor the progress of the Codex Records
  being created.
</aside>


## Convert Metadata XML into JSON

It may be easier for your application to export metadata as XML instead of JSON,
so a utility route exists to simply convert an XML file into a JSON response.
You can then use this JSON response as the `metadata` array when
[creating Codex Records in bulk](#create-codex-records-in-bulk).

<aside class="success">
  The Codex Viewer has an option to do this action in-browser for auction house
  accounts.
</aside>

```javascript
import fs from 'fs'
import request from 'request'

const convertXMLRequestOptions = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/client/bulk-transactions/record-mint/convert/xml',
  method: 'post',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  // by using "formData" here instead of "body", request will send this request
  //  as multipart/form-data, which is necessary for sending file data
  formData: {
    mainImage: fs.createReadStream('/tmp/xml-metadata.xml'),
  },
}

request(convertXMLRequestOptions, (convertXMLError, convertXMLResponse) => {

  // now take the JSON metadata and send it to the bulk transaction route...
  const metadata = convertXMLResponse.body.result

  const createBulkTransactionRequestOptions = {
    url: 'https://rinkeby-api.codexprotocol.com/v1/client/bulk-transaction/record-mint',
    method: 'post',
    json: true,

    headers: {
      Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
    },

    body: {
      metadata,
    },
  }

  request(createBulkTransactionRequestOptions, (createBulkTransactionError, createBulkTransaction) => {
    console.log(createBulkTransaction.body.result) // a Bulk Transaction object
  })

})
```

> The above API sends an XML file, retrieves converted JSON in the response, and sends that JSON metadata to the [create Codex Records in Bulk](#create-codex-records-in-bulk) route.

### HTTP Request

`POST /v1/client/bulk-transactions/record-mint/convert/xml`

### Request Parameters

Parameter | Type      | Description
--------- | --------- | --------------------------------------------------------
metadata  | File Data | The XML file to convert.

<!--
  the previous code example is hella long, and the spacing looks weird with the
  upcoming code example right next to it, so this is kind of a hacky way to push
  down this subheader so that it's code example isn't nestled up next to the
  previous one
  -->
<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>

### Example XML Structure

```xml
<Records>
  <Record>
    <!-- required -->
    <Name>Really Cool Codex Record #1</Name>
    <IsPrivate>false</IsPrivate>
    <IsHistoricalProvenancePrivate>false</IsHistoricalProvenancePrivate>
    <Description>This is a really cool Codex Record!</Description>
    <MainImageUrl>https://example.com/images/cool-main-image.jpg</MainImageUrl>

    <!-- optional additional images -->
    <ImageUrl>https://example.com/images/cool-supplemental-image-1.jpg</ImageUrl>
    <ImageUrl>https://example.com/images/cool-supplemental-image-2.jpg</ImageUrl>

    <!-- optional additional files -->
    <FileUrl>https://example.com/files/author-notes.txt</FileUrl>
    <FileUrl>https://example.com/files/certificate-of-authenticity.pdf</FileUrl>

    <!-- optional -->
    <AdditionalMetadata>
      <LotNumber>123A</LotNumber>
      <SalePrice>1000</SalePrice>
      <SaleCurrency>USD</SaleCurrency>
      <Condition>Excellent</Condition>
      <EditionNumber>16</EditionNumber>
      <TotalEditions>64</TotalEditions>
      <Medium>mixed media on paper</Medium>
      <CreatorName>Cool Guy McGee</CreatorName>
      <AuctionName>My Cool Auction</AuctionName>
      <DimensionsInches>18 x 23</DimensionsInches>
      <AssetSoldAt>2019-03-21T16:30:06.030Z</AssetSoldAt>
      <SignatureLocation>front, top left</SignatureLocation>
      <AssetCreatedAt>1993-01-01T00:00:00.000Z</AssetCreatedAt>
      <DimensionsCentimeters>45.7 x 58.4</DimensionsCentimeters>
    </AdditionalMetadata>

    <AuctionHouseMetadata>
      <!-- required -->
      <Id>1234</Id>

      <!-- optional -->
      <LinkbackUrl>https://www.heffel.com/auction/Details_E.aspx?ID=30510</LinkbackUrl>

      <!-- any other arbitrary fields -->
      <TransactionID>27299</TransactionID>
      <InventoryNumber>TWC1123-G16926-001</InventoryNumber>
    </AuctionHouseMetadata>
  </Record>

  <!-- more <Record> elements here -->

</Records>
```

The structure of XML metadata is very similar to the structure of JSON metadata.
Please see the example in this section to guide you when constructing your XML
metadata.


## Get a Bulk Transaction

This endpoint can be used to retrieve a specific Bulk Transaction that your
application has created. This data can be used to monitor the progress of an
in-progress Bulk Transaction, or to retrieve stats about an already-completed
Bulk Transaction (such as how many Codex Records were created.)

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/client/bulk-transactions/5c8fe94329de5e47fb9df266',
  method: 'get',
  json: true,
}

request(options, (error, response) => {
  console.log(response.body.result) // Bulk Transaction with id 5c8fe94329de5e47fb9df266
})
```

> The above API call returns a single [Bulk Transaction](#bulk-transaction).

### HTTP Request

`GET /v1/client/bulk-transactions/:bulkTransactionId`

### URL Parameters

Parameter         | Type   | Description
----------------- | ------ | ---------------------------------------------------
bulkTransactionId | String | The `id` of the Bulk Transaction to retrieve.


## Get All Bulk Transactions

This endpoint can be used to retrieve all Bulk Transactions that your
application has created. This data can be used to monitor the progress of
in-progress Bulk Transactions, or to retrieve stats about a already-completed
Bulk Transactions (such as how many Codex Records were created.)

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-api.codexprotocol.com/v1/client/bulk-transactions',
  method: 'get',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  // sort by date created in reverse and get 5 records, skipping the first ten
  body: {
    limit: 5,
    offset: 10,
    order: '-createdAt',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // an array of your application's Bulk Transactions
})
```

> The above API call returns an array of [Bulk Transactions](#bulk-transaction).

### HTTP Request

`GET /v1/client/bulk-transactions`

### Request Parameters

Parameter    | Type   | Default    | Description
------------ | ------ | ---------- | --------------------------------------------
limit        | Number | 100        | How many records to retrieve. Use with `offset` to paginate through Bulk Transactions.
offset       | Number | 0          | How many records to skip before applying the `limit`. Use with `limit` to paginate through Bulk Transactions.
order        | String | -createdAt | To sort in chronological order, specify this value as `createdAt`.
